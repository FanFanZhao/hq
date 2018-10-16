<template>
	<div class="k-line-area bg-1d1d29">
		<!--<input id="text" />-->
		<!--<div @click="chose" style="color: #fff">322332</div>-->
		<div id="tv_chart_container" style="width:100%;height: 400px"></div>
	</div>
</template>

<script>

	// import Datafeeds from "../assets/js/datafeed.js";

	export default {
		name: "tv",

		data() {
			return {
				widget: null,
				symbolInfo: null,
				feed: null,
				wsEx:null,
				ws:null,
				lists:[],
				newData:'',
				url:'ws://58.218.68.152:8800/',
				last_price:100
			}
		},
		created(){

		},
		props:['renew','getupdata'],
		watch: {
			'renew': {
				handler(newval, old) {

					// console.log(typeof newval)
					// this.getSymbol(newval)
					this.widget.setSymbol(newval,1,function onReadyCallback(){})
					this.$store.state.symbol=newval

					this.ws.send(JSON.stringify({'cmd':'band','mid': this.$store.state.id}))
				}
			},
			'getupdata':{
				handler(newval, old) {
					//   console.log(newval,'0000')
					this.newData=newval[0]

					// console.log( this.newData,'99999')
				}
			}
		},
		mounted() {
			this.coin && this.coin.virtualCoinId && this.updateWidget(this.coin);

			this.createWidget()

		},
		destroyed() {
			this.removeWidget();
		},
		beforeDestroy(){
			this.ws.close()

		},

		methods: {

			createWebSocket(url) {
				let th=this
				let id=th.$store.state.id
				try{
					if('WebSocket' in window){
						th.ws = new WebSocket(url);
					}else if('MozWebSocket' in window){
						th.ws = new MozWebSocket(url);
					}
					th.ws.onopen = function() {
						heartCheck.start()
						th.ws.send(JSON.stringify({'cmd':'band','mid':id}))
						//('已连接')
					};
					let heartCheck = {
						timeout: 15000,
						timeoutObj: null,
						serverTimeoutObj: null,
						start: function(){
							console.log(this);
							let self = this;
							this.timeoutObj && clearTimeout(this.timeoutObj);
							this.serverTimeoutObj && clearTimeout(this.serverTimeoutObj);
							this.timeoutObj = setInterval(function(){
								//这里发送一个心跳，后端收到后，返回一个心跳消息，
								let timed=new Date().getTime()
								th.ws.send(JSON.stringify({'cmd':'ping','args':[timed]}));
							}, this.timeout)
						}
					}
				}catch(e){
					console.log(e);
				}
			},
			createFeed(){
				let this_vue = this;
				let Datafeed = {};
				//  this_vue.ws=new WebSocket('ws://58.218.68.152:8800/')


				this_vue.createWebSocket(this_vue.url)
				console.log(this_vue.ws)

				Datafeed.DataPulseUpdater = function(datafeed, updateFrequency) {
					this._datafeed = datafeed;
					this._subscribers = {};

					this._requestsPending = 0;
					var that = this;

					var update = function() {
						if (that._requestsPending > 0) {
							return;
						}

						for (var listenerGUID in that._subscribers) {
							var subscriptionRecord = that._subscribers[listenerGUID];
							var resolution = subscriptionRecord.resolution;

							var datesRangeRight = parseInt((new Date().valueOf()) / 1000);

							//	BEWARE: please note we really need 2 bars, not the only last one
							//	see the explanation below. `10` is the `large enough` value to work around holidays
							var datesRangeLeft = datesRangeRight - that.periodLengthSeconds(resolution, 10);

							that._requestsPending++;

							(function(_subscriptionRecord) { // eslint-disable-line
								that._datafeed.getBars(_subscriptionRecord.symbolInfo, resolution, datesRangeLeft, datesRangeRight, function(bars) {
										that._requestsPending--;

										//	means the subscription was cancelled while waiting for data
										if (!that._subscribers.hasOwnProperty(listenerGUID)) {
											return;
										}

										if (bars.length === 0) {
											return;
										}

										var lastBar = bars[bars.length - 1];
										if (!isNaN(_subscriptionRecord.lastBarTime) && lastBar.time < _subscriptionRecord.lastBarTime) {
											return;
										}

										var subscribers = _subscriptionRecord.listeners;

										//	BEWARE: this one isn't working when first update comes and this update makes a new bar. In this case
										//	_subscriptionRecord.lastBarTime = NaN
										var isNewBar = !isNaN(_subscriptionRecord.lastBarTime) && lastBar.time > _subscriptionRecord.lastBarTime;

										//	Pulse updating may miss some trades data (ie, if pulse period = 10 secods and new bar is started 5 seconds later after the last update, the
										//	old bar's last 5 seconds trades will be lost). Thus, at fist we should broadcast old bar updates when it's ready.
										if (isNewBar) {
											if (bars.length < 2) {
												throw new Error('Not enough bars in history for proper pulse update. Need at least 2.');
											}

											var previousBar = bars[bars.length - 2];
											for (var i = 0; i < subscribers.length; ++i) {
												subscribers[i](previousBar);
											}
										}

										_subscriptionRecord.lastBarTime = lastBar.time;

										for (var i = 0; i < subscribers.length; ++i) {
											subscribers[i](lastBar);
										}
									},

									//	on error
									function() {
										that._requestsPending--;
									});
							})(subscriptionRecord);
						}
					};

					if (typeof updateFrequency != 'undefined' && updateFrequency > 0) {
						setInterval(update, updateFrequency);
					}
				};

				Datafeed.DataPulseUpdater.prototype.periodLengthSeconds = function(resolution, requiredPeriodsCount) {
					var daysCount = 0;

					if (resolution === 'D') {
						daysCount = requiredPeriodsCount;
					} else if (resolution === 'M') {
						daysCount = 31 * requiredPeriodsCount;
					} else if (resolution === 'W') {
						daysCount = 7 * requiredPeriodsCount;
					} else {
						daysCount = requiredPeriodsCount * resolution / (24 * 60);
					}

					return daysCount * 24 * 60 * 60;
				};

				Datafeed.DataPulseUpdater.prototype.subscribeDataListener = function(symbolInfo, resolution, newDataCallback, listenerGUID) {
					this._datafeed._logMessage('Subscribing ' + listenerGUID);

					if (!this._subscribers.hasOwnProperty(listenerGUID)) {
						this._subscribers[listenerGUID] = {
							symbolInfo: symbolInfo,
							resolution: resolution,
							lastBarTime: NaN,
							listeners: []
						};
					}

					this._subscribers[listenerGUID].listeners.push(newDataCallback);
				};

				Datafeed.DataPulseUpdater.prototype.unsubscribeDataListener = function(listenerGUID) {
					this._datafeed._logMessage('Unsubscribing ' + listenerGUID);
					delete this._subscribers[listenerGUID];
				};

				Datafeed.Container = function(updateFrequency){
					this._configuration = {
						supports_search: false,
						supports_group_request: false,
						supported_resolutions: ['1', '3', '5', '15', '30', '60', '120', '240', '360', '720', '1D', '3D', '1W', '1M'],
						supports_marks: true,
						supports_timescale_marks: true,
						exchanges: ['gh']
					};

					this._barsPulseUpdater = new Datafeed.DataPulseUpdater(this, updateFrequency || 10 * 1000);
					// this._quotesPulseUpdater = new Datafeed.QuotesPulseUpdater(this);

					this._enableLogging = true;
					this._callbacks = {};

					this._initializationFinished = true;
					this._fireEvent('initialized');
					this._fireEvent('configuration_ready');
				};

				Datafeed.Container.prototype._fireEvent = function(event, argument) {
					if (this._callbacks.hasOwnProperty(event)) {
						var callbacksChain = this._callbacks[event];
						for (var i = 0; i < callbacksChain.length; ++i) {
							callbacksChain[i](argument);
						}

						this._callbacks[event] = [];
					}
				};

				Datafeed.Container.prototype._logMessage = function(message) {
					if (this._enableLogging) {
						var now = new Date();
						console.log("CHART LOGS: "+now.toLocaleTimeString() + '.' + now.getMilliseconds() + '> ' + message);
					}
				};

				Datafeed.Container.prototype.on = function(event, callback) {
					if (!this._callbacks.hasOwnProperty(event)) {
						this._callbacks[event] = [];
					}

					this._callbacks[event].push(callback);
					return this;
				};

				Datafeed.Container.prototype.onReady = function(callback) {
					let that = this;
					if (this._configuration) {
						setTimeout(function() {
							callback(that._configuration);
						}, 0);
					}
					else {
						this.on('configuration_ready', function() {
							callback(that._configuration);
						});
					}
				};

				Datafeed.Container.prototype.resolveSymbol = function(symbolName, onSymbolResolvedCallback, onResolveErrorCallback) {
					this._logMessage("GOWNO :: resolve symbol "+ symbolName);
					Promise.resolve().then(() => {
						console.log(this_vue.$store.state.priceScale,'12345s313123122adaslast')
						function adjustScale(){
							if(this_vue.last_price.length==8)
								return 100*1000000;
							else
								return 100;
						}

						// this._logMessage("GOWNO :: onResultReady inject "+'AAPL');
						console.log(this_vue.$store.state.priceScale,'123stf')
						onSymbolResolvedCallback({
							"name": this_vue.$store.state.symbol,
							"timezone": "Asia/Shanghai",
							"pricescale": this_vue.$store.state.priceScale,
							"minmov": 1, //minmov(最小波动), pricescale(价格精度), minmove2, fractional(分数)
							"minmov2": 0,//这是一个神奇的数字来格式化复杂情况下的价格。
							"ticker": this_vue.$store.state.symbol,
							"description": "",
							"type": "bitcoin",
							"volume_precision": 8,
							// "exchange-traded": "sdt",
							// "exchange-listed": "sdt",
							//现在，这两个字段都为某个交易所的略称。将被显示在图表的图例中，以表示此商品。目前此字段不用于其他目的。
							"has_intraday": true,
							// "intraday_multipliers": ['1'],  //不要删，(如果设置了这个，那么tv会默认把时间范围缩到一天前，而不能调取从后台返回的数据)
							//It is an array containing intraday resolutions (in minutes) the datafeed wants to build by itself. E.g.,
							// if the datafeed reported he supports resolutions ["1", "5", "15"], but in fact it has only 1 minute bars for symbol X,
							// it should set intraday_multipliers of X = [1]. This will make Charting Library to build 5 and 15 resolutions by itself.
							"has_weekly_and_monthly": true,
							"has_no_volume": false, //布尔表示商品是否拥有成交量数据。
							'session': '24x7',
							'supported_resolutions': ['1', '3', '5', '15', '30', '60', '120', '240', '360', '720', '1D', '3D', '1W', '1M']

						});
					})
				};


				//初始化数据
				Datafeed.Container.prototype.getBars = function(symbolInfo, resolution, rangeStartDate, rangeEndDate, onHistoryCallback, onErrorCallback) {
					// if (rangeStartDate > 0 && (rangeStartDate + '').length > 10) {
					//   throw new Error(['Got a JS time instead of Unix one.', rangeStartDate, rangeEndDate]);
					// }

					if(resolution.indexOf('D')==-1&&resolution.indexOf('W')==-1&&resolution.indexOf('M')==-1){
						resolution=resolution+'min'
					}else if(resolution.indexOf('W')!=-1||resolution.indexOf('M')!=-1){
						resolution=resolution
					}
					$.ajax({
						url:'http://test1.fuwuqian.cn/api/currency/timeshar_test?' +
						'from='+rangeStartDate+'&to='+rangeEndDate+'&symbol=imc/cny&period='+resolution,
						//test1.fuwuqian.cn/api/currency/timeshar_test?start=0&end=999999999999999999&symbol=imc/cny&period=1min
						// url:'http://172.16.9.97/index.php/get_kline_data?type=sdt2btc&time='+resolution+'&data_type=v1',
						// url:'http://test1.fuwuqian.cn/api/currency/timeshar_test',
						type:'get',
						// params:{
						// 	from: rangeStartDate,
						// 	to:rangeEndDate,
						// 	symbol:'imc/cny' ,
						// 	period:resolution
						// },
						success: function(data){
							console.log(data.data[0].high,'99')
							if(data.data.length>0){
								data.data.forEach((item,i)=>{
									item.open=Number(item.open)
									item.close=Number(item.close)
									item.high=Number(item.high)
									item.low=Number(item.low)
									item.volume=Number(item.volume)
								})
								onHistoryCallback(data.data,{noData:false})
								onHistoryCallback([],{noData:true})
							}else{
								onHistoryCallback([],{noData:true})
							}


							// onLoadedCallback([])

						}
					})

				};
				//实时数据
				Datafeed.Container.prototype.subscribeBars = function(symbolInfo, resolution, onRealtimeCallback, listenerGUID, onResetCacheNeededCallback) {

					this_vue.ws.onmessage = function (res) {

						if(JSON.parse(res.data).type=="kline"){

							let bar=JSON.parse(res.data).data[0]
							onRealtimeCallback(bar)
						}
						if(JSON.parse(res.data).type=='"kline"'){
						}

					}
					this_vue.ws.onclose = function(){

						this_vue.createWebSocket(this_vue.url)
					}


					// this_vue.bars.forEach(function (bar) { // in subscribeBars
					//   onRealtimeCallback(bar)
					// });
					// this.on('pair_change', function() {
					//   onResetCacheNeededCallback();
					// });
					//this._barsPulseUpdater.subscribeDataListener(symbolInfo, resolution, onRealtimeCallback, listenerGUID, onResetCacheNeededCallback);
				};

				Datafeed.Container.prototype.unsubscribeBars = function(listenerGUID) {

					this._barsPulseUpdater.unsubscribeDataListener(listenerGUID);

				};

				return new Datafeed.Container;
			},
			updateData(data) {
				if (data) {
					console.log("emit real-time");
					this.$emit('real-time', data);
				}
			},
			createWidget() {
				let _this = this;

				this.$nextTick(function () {
					let widget =_this.widget= new TradingView.widget({
						symbol:_this.$store.state.symbol,
						interval: 1,
						debug: false,
						fullscreen: false,
						autosize: true,
						container_id: "tv_chart_container",
						// datafeed: new Datafeeds.UDFCompatibleDatafeed("http://demo_feed.tradingview.com"),
						datafeed:_this.createFeed(),
						library_path: "/static/tradeview/charting_library/",
						custom_css_url: 'bundles/new.css',
						locale: "zh",
						width: "100%",
						height: 516,
						drawings_access: { type: 'black', tools: [ { name: "Regression Trend" } ] },
						disabled_features: [  //  禁用的功能
							'left_toolbar', 'header_indicators', 'header_saveload', 'compare_symbol', 'display_market_status',
							'go_to_date', 'header_chart_type', 'header_compare', 'header_interval_dialog_button',
							'header_resolutions', 'header_screenshot', 'header_symbol_search', 'header_undo_redo',
							'legend_context_menu', 'show_hide_button_in_legend', 'show_interval_dialog_on_key_press',
							'snapshot_trading_drawings', 'symbol_info', 'timeframes_toolbar', 'use_localstorage_for_settings',
							'volume_force_overlay'
						],
						enabled_features: [ //  启用的功能（备注：disable_resolution_rebuild 功能用于控制当时间范围为1个月时，日期刻度是否都是每个月1号
							'dont_show_boolean_study_arguments', 'hide_last_na_study_output', 'move_logo_to_main_pane',
							'same_data_requery', 'side_toolbar_in_fullscreen_mode', 'disable_resolution_rebuild'
						],
						charts_storage_url: 'http://saveload.tradingview.com',
						charts_storage_api_version: "1.1",
						toolbar_bg: "transparent",
						timezone: "Asia/Shanghai",
						studies_overrides: {
							'volume.precision': '1000'
						},
						overrides: _this.overrides()
					});

					widget.MAStudies = [];
					widget.selectedIntervalButton = null;
					// widget.setLanguage('en')
					widget.onChartReady(function() { //图表方法
						// document.getElementById('trade-view').childNodes[0].setAttribute('style', 'display:block;width:100%;height:100%;');
						//let that =this

						// widget.chart().createStudy('Moving Average', false, true, [15,'close', 0],null,{'Plot.color':'#fff'});
						let buttonArr = [
							{
								value: "1min",
								period: "1",
								text: "分时",
								chartType: 3
							},
							{
								value: "1",
								period: "1m",
								text: "1分钟",
								chartType:1
							},
							{
								value: "5",
								period: "5m",
								text: "5分钟",
								chartType: 1
							},
							{
								value: "15",
								period: "15m",
								text: "15分钟",
								chartType: 1
							},
							{
								value: "30",
								period: "30m",
								text: "30分钟",
								chartType:1
							},
							{
								value: "60",
								period: "60分钟",
								text: "1小时",
								chartType: 1
							},
							{
								value: "1D",
								period: "1D",
								text: "1天",
								chartType: 1
							},
							{
								value: "1W",
								period: "1W",
								text: "1周",
								chartType:1
							}, {
								value: "1M",
								period: "1M",
								text: "1月",
								chartType: 1
							}
						];
						let btn = {};
						let nowTime = '';
						let handleClick = (e, value) => {
							console.log(value)
							_this.setSymbol = function(symbol,value){
								gh.chart().setSymbol(symbol,value);
							};

							widget.chart().setResolution(value,function onReadyCallback(){});  //改变分辨率

							$(e.target)
								.addClass("mydate")
								.closest("div.space-single")
								.siblings("div.space-single")
								.find("div.button")
								.removeClass("mydate")

						};

						buttonArr.forEach((v, i) => {
							// console.log(v)
							let  button=widget.createButton()
							button.attr('title',v.text)
								.addClass("my2")
								.text(v.text)

							if(v.text=='1分钟'){
								button.css({"background-color":"#4e5b85",'color':'#fff'})
							}

							// console.log($(this),'999999')
							btn = button.on("click", function (e) {
								$(this).parents(".left").children().find(".my2").removeAttr("style"); //去掉1分钟的
								handleClick(e, v.value);

								widget.chart().setChartType(v.chartType); //改变K线类型
							});

						});

					});
//           widget.onChartReady(function () {
//             let chart = widget.chart();
//
//
//             // mas.forEach(item => {
//             // chart.createStudy("Moving Average", false, false, [item.day], entity => {
//             //   widget.MAStudies.push(entity);
//             // }, {"plot.color": item.color});
//             // });
//
//             chart.onIntervalChanged().subscribe(null, function (interval, obj) {
//               widget.changingInterval = false;
//             });
//
//             buttons.forEach((item, index) => {
//               let button = widget.createButton();
//
//               // item.resolution === widget.options.interval && updateSelectedIntervalButton(button);
//               button.attr("data-resolution", item.resolution)
//                 .attr("data-chart-type", item.chartType === undefined ? 1 : item.chartType)
//                 .html("<span>"+ item.label +"</span>")
//                 .on("click", function() {
//                   console.log(item.resolution)
//
//
//                   if (!widget.changingInterval && !button.hasClass("selected")) {
//                     let chartType = +button.attr("data-chart-type");
//                     let resolution = button.attr("data-resolution");
//
//                     if (chart.resolution() !== item.resolution) {
//                       widget.chart().setResolution(item.resolution,function onReadyCallback(){});
//                       // alert(item.resolution)
//                       // chart.setResolution(item.resolution,function onReadyCallback(){});
//                       // widget.changingInterval = true;
//
//                     }
//                     if (chart.chartType() !== chartType) {
//                       chart.setChartType(chartType);
// //											widget.applyOverrides({
// //												"mainSeriesProperties.style": chartType
// //											});
//                     }
//                     updateSelectedIntervalButton(button);
//                     showMAStudies(chartType !== 3);
//                   }
//                 })
//             });
//
//             function updateSelectedIntervalButton(button) {
//               console.log(button)
//               widget.selectedIntervalButton && widget.selectedIntervalButton.removeClass("selected");
//               button.addClass("selected");
//               widget.selectedIntervalButton = button;
//             }
//
//             function showMAStudies(visible) {
//               widget.MAStudies.forEach(item => {
//                 chart.setEntityVisibility(item, visible);
//               })
//             }
//           });

					_this.widget = widget;
				})
			},
			updateWidget(item) {
				this.symbolInfo = {
					name: item,
					ticker: item,
					description: "",
					session: "24x7",
					supported_resolutions: ['1', '5', '15', '30', '60', '240', '1D', '3D', '1W', '1M'],
					has_intraday: true,
					has_daily: true,
					has_weekly_and_monthly: true,
					timezone: "UTC",
				}

				this.removeWidget();
				this.createWidget();
			},
			removeWidget() {
				if (this.widget) {
					this.widget.remove();
					this.widget = null;
				}
			},
			overrides() {
				let style = {
					up: "#589065",
					down: "#AE4E54",
					bg: "#181b2a",
					grid: "#1E2740",
					cross: "#1E2740",
					border: "#4e5b85",
					text: "#61688A",
					areatop: "rgba(122, 152, 247, .1)",
					areadown: "rgba(122, 152, 247, .02)"
				};
				return{
					'volumePaneSize': "large", //large, medium, small, tiny
					'paneProperties.topMargin':'20',
					"scalesProperties.lineColor": style.text,
					"scalesProperties.textColor": style.text,
					"paneProperties.background": style.bg,//改变背景色的重要代码
					"paneProperties.vertGridProperties.color": style.grid,
					"paneProperties.horzGridProperties.color": style.grid,
					"paneProperties.crossHairProperties.color": style.cross,
					"paneProperties.legendProperties.showLegend": true,
					"paneProperties.legendProperties.showStudyArguments": true,
					"paneProperties.legendProperties.showStudyTitles": true,
					"paneProperties.legendProperties.showStudyValues": true,
					"paneProperties.legendProperties.showSeriesTitle": true,
					"paneProperties.legendProperties.showSeriesOHLC": true,
					"mainSeriesProperties.candleStyle.upColor": style.up,
					"mainSeriesProperties.candleStyle.downColor": style.down,
					"mainSeriesProperties.candleStyle.drawWick": true,
					"mainSeriesProperties.candleStyle.drawBorder": true,
					"mainSeriesProperties.candleStyle.borderColor": style.border,
					"mainSeriesProperties.candleStyle.borderUpColor": style.up,
					"mainSeriesProperties.candleStyle.borderDownColor": style.down,
					"mainSeriesProperties.candleStyle.wickUpColor": style.up,
					"mainSeriesProperties.candleStyle.wickDownColor": style.down,
					"mainSeriesProperties.candleStyle.barColorsOnPrevClose": false,
					"mainSeriesProperties.hollowCandleStyle.upColor": style.up,
					"mainSeriesProperties.hollowCandleStyle.downColor": style.down,

					"mainSeriesProperties.hollowCandleStyle.drawWick": true,
					"mainSeriesProperties.hollowCandleStyle.drawBorder": true,
					"mainSeriesProperties.hollowCandleStyle.borderColor": style.border,
					"mainSeriesProperties.hollowCandleStyle.borderUpColor": style.up,
					"mainSeriesProperties.hollowCandleStyle.borderDownColor": style.down,
					"mainSeriesProperties.hollowCandleStyle.wickColor": style.line,
					"mainSeriesProperties.haStyle.upColor": style.up,
					"mainSeriesProperties.haStyle.downColor": style.down,
					"mainSeriesProperties.haStyle.drawWick": true,
					"mainSeriesProperties.haStyle.drawBorder": true,
					"mainSeriesProperties.haStyle.borderColor": style.border,
					"mainSeriesProperties.haStyle.borderUpColor": style.up,
					"mainSeriesProperties.haStyle.borderDownColor": style.down,
					"mainSeriesProperties.haStyle.wickColor": style.border,
					"mainSeriesProperties.haStyle.barColorsOnPrevClose": false,
					"mainSeriesProperties.barStyle.upColor": style.up,
					"mainSeriesProperties.barStyle.downColor": style.down,
					"mainSeriesProperties.barStyle.barColorsOnPrevClose": false,
					"mainSeriesProperties.barStyle.dontDrawOpen": false,
					"mainSeriesProperties.lineStyle.color": style.border,
					"mainSeriesProperties.lineStyle.linewidth": 1,
					"mainSeriesProperties.lineStyle.priceSource": "close",
					"mainSeriesProperties.areaStyle.color1": style.areatop,
					"mainSeriesProperties.areaStyle.color2": style.areadown,
					"mainSeriesProperties.areaStyle.linecolor": style.border,
					"mainSeriesProperties.areaStyle.linewidth": 1,
					"mainSeriesProperties.areaStyle.priceSource": "close"
				}
				// this.sty2()
			},


			chose(){
				// console.log(this.widget,'000')
				this.widget.setLanguage('en') //设置语言

				// this.$store.commit('symbol','sdt')
				// this.$store.state.symbol='sdt6456' //切换交易对
			}
		},


	}
</script>
