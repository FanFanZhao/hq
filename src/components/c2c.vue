<template>
    <div id="c2c-box" class="flex">
        <div class="c2c-l">
            <ul>
                <li class="flex" v-for="(item,index) in currency_list" :key="index" :class="index == active?'bg_active':''" :data-id="item.id" @click="currency_click(item.id,item.name,index)">
                    <div class="flex">
                        <div>{{item.name}}/CNY</div>
                        <div class="redColor">{{item.name}}/人民币</div>
                    </div>
                    <span>></span>
                </li>
            </ul>
        </div>
        <div class="c2c-r">
            <div class="top">
                <div class="top-title flex">
                    <div>
                        <router-link tag="span" to="/c2c" class="link-span">tether {{currency_name}}</router-link>
                        <span>对cny</span>
                    </div>
                </div>
                <div class="top-price flex">
                    <div>
                        <span>实时价：</span>
                        <span>￥6.83</span>
                    </div>
                    <div>
                        <span>日涨跌：</span>
                        <span>1.2%</span>
                    </div>
                    <div>
                        <span>最高价：</span>
                        <span>￥6.83</span>
                    </div>
                    <div>
                        <span>最低价：</span>
                        <span>￥6.83</span>
                    </div>
                </div>
                <div class="inp-items flex">
                    <div class="inp-item">
                        <div class="inp-title flex">
                            <div>买入{{currency_name}}</div>
                            
                        </div>
                        <div class="how redColor ft14">如何买入?</div>
                        <div class="three-inp">
                            <div class="inp-box">
                                <span>买入估价CNY</span>
                                <div style="background:#f8f8f8;">6.85</div>
                            </div>
                            <div class="inp-box">
                                <span>买入量{{currency_name}}</span>
                                <input type="text" v-model="num">
                            </div>
                            <div class="inp-box">
                                <span>金额CNY</span>
                                <input type="text" v-model="price">
                            </div>
                            <div class="inp-box">
                                <span>姓名</span>
                                <input type="text" v-model="user_name">
                            </div>
                            <div class="inp-box">
                                <span>详细内容</span>
                                <input type="text" v-model="content">
                            </div>
                        </div>
                        <div class="pay-opts flex">
                            <div>支付方式</div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay" value="支付宝" v-model="pay">
                                    支付宝
                                </label>
                            </div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay" value="微信" v-model="pay">
                                    微信
                                </label>
                            </div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay" value="银行卡" v-model="pay">
                                    银行转账
                                </label>
                            </div>
                            <div>（必须本人支付)</div>
                            <router-link tag="div" to="/c2c">《交易须知》</router-link>
                        </div>
                        <div class="btn-in bgRed" @click="bui_in">买入（CNY→USDT）</div>
                    </div>
                    <div class="inp-item">
                        <div class="inp-title flex">
                            <div>买入{{currency_name}}</div>
                            <div>
                                <router-link to="/c2c">{{currency_name}}充值</router-link>
                                <router-link to="/c2c">{{currency_name}}提现</router-link>
                            </div>
                        </div>
                        <div class="how redColor ft14">如何卖出?</div>
                        <div class="three-inp">
                            <div class="inp-box">
                                <span>卖出估价CNY</span>
                                <div style="background:#f8f8f8;color:#008069">6.85</div>
                            </div>
                            <div class="inp-box">
                                <span>卖出量{{currency_name}}</span>
                                <input type="text">
                            </div>
                            <div class="inp-box">
                                <span>金额CNY</span>
                                <input type="text">
                            </div>
                             <div class="inp-box">
                                <span>姓名</span>
                                <input type="text" v-model="user_name">
                            </div>
                            <div class="inp-box">
                                <span>详细内容</span>
                                <input type="text" v-model="content">
                            </div>
                        </div>
                        <div class="pay-opts flex">
                            <div>支付方式</div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay">
                                    支付宝
                                </label>
                            </div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay">
                                    微信
                                </label>
                            </div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay">
                                    银行转账
                                </label>
                            </div>
                            
                            <div>（必须本人支付)</div>
                            <router-link tag="div" to="/c2c">《交易须知》</router-link>
                        </div>
                        <div class="btn-out">卖出（USDT→CNY）</div>

                    </div>
                    
                </div>
            </div>
            <div class="bot">
                <div class="bot-title flex">
                    <div>市场挂单（只显示在线商家）</div>
                    <div>显示市场挂单</div>
                </div>
                <div class="list-title flex">
                    <div>类型</div>
                    <div>价格(CNY)</div>
                    <div>数量(USDT)</div>
                    <div>总计(CNY)</div>
                    <div>交易限额(USDT)</div>
                    <div>商家在线</div>
                    <div>成交单数</div>
                    <div>平均用时</div>
                    <div>付款方式</div>
                    <div style="visibility: hidden;"></div>
                </div>
                <ul class="ul-out">
                    <li class="flex">
                        <div>卖出</div>
                        <div>6.85</div>
                        <div>3660</div>
                        <div>2550</div>
                        <div>5100-1000</div>
                        <div>付**3分钟</div>
                        <div>175</div>
                        <div>1分钟</div>
                        <div>支银</div>
                        <div>买入</div>
                    </li>
                    <li class="flex">
                        <div>卖出</div>
                        <div>6.85</div>
                        <div>3660</div>
                        <div>2550</div>
                        <div>5100-1000</div>
                        <div>付**3分钟</div>
                        <div>175</div>
                        <div>1分钟</div>
                        <div>支银</div>
                        <div>买入</div>
                    </li>
                    <li class="flex">
                        <div>卖出</div>
                        <div>6.85</div>
                        <div>3660</div>
                        <div>2550</div>
                        <div>5100-1000</div>
                        <div>付**3分钟</div>
                        <div>175</div>
                        <div>1分钟</div>
                        <div>支银</div>
                        <div>买入</div>
                    </li>
                </ul>
                <ul class="ul-in">
                    <li class="flex">
                        <div>卖出</div>
                        <div>6.85</div>
                        <div>3660</div>
                        <div>2550</div>
                        <div>5100-1000</div>
                        <div>付**3分钟</div>
                        <div>175</div>
                        <div>1分钟</div>
                        <div>支银</div>
                        <div>买入</div>
                    </li>
                    <li class="flex">
                        <div>卖出</div>
                        <div>6.85</div>
                        <div>3660</div>
                        <div>2550</div>
                        <div>5100-1000</div>
                        <div>付**3分钟</div>
                        <div>175</div>
                        <div>1分钟</div>
                        <div>支银</div>
                        <div>买入</div>
                    </li>
                    <li class="flex">
                        <div>卖出</div>
                        <div>6.85</div>
                        <div>3660</div>
                        <div>2550</div>
                        <div>5100-1000</div>
                        <div>付**3分钟</div>
                        <div>175</div>
                        <div>1分钟</div>
                        <div>支银</div>
                        <div>买入</div>
                    </li>
                </ul>
                
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data(){
        return {
            token:'',
            listIn:{page:1,list:[],hasMore:true},
            listOut:{page:1,list:[],hasMore:true},
            active:0,
            currency_list:[],
            currency_name:'',
            id:'',
            price:'',
            num:'',
            pay:'',
            user_name:'',
            content:'' 
        }
    },
    created(){
        this.token = window.localStorage.getItem('token')||'';
        this.getList();
        this.get_currency();
        this.getList(1);
        this.getList(0);
    },
    methods:{
        get_currency(){
            this.$http({
                url: "/api/currency/list",
                method: "get",
                headers: {'Authorization':  this.token},
            }).then(res => {
                console.log(res);
                if (res.data.type == "ok") {
                this.currency_list = res.data.message.legal;
                this.currency_name = res.data.message.legal[0].name;
                this.id = res.data.message.legal[0].id;
                }
            });
        },
        //选择币种
        currency_click(id,name,index){
           this.currency_name = name;
           this.active = index;
           this.id = id;
        },
        getList(type){
            let page = type == 1?this.listOut.page:this.listIn.page;
            this.$http({
                url:'/api/c2c/list?type='+type+'&page='+page,
               
                method:'get',
                headers:{'Authorization':this.token}
            }).then(res => {
                if(res.data.type == 'ok'){
                    let list = res.data.message.list;
                    if(list.length != 0){
                        if(type == 1){
                            this.listOut.list.concat(list);
                            this.listOut.page++;
                        } else {
                            this.listIn.concat(list);
                            this.listIn.page++;
                        }
                    } else {
                        type == 1?this.listOut.hasMore = false:this.listIn.hasMore = false;
                    }
                }
                
            })
        },
        //买入
        bui_in(){
            this.$http({
                url: "/api/c2c/add",
                method: "post",
                data:{
                    price:this.price,
                    number:this.num,
                    name:this.user_name,
                    pay_mode:'微信',
                    content:this.content,
                    token:this.currency_name
                },
                headers: {'Authorization':  this.token}
            }).then(res => {
                console.log(res);
                if (res.data.type == "ok" && res.data.message.length != 0) {
                this.quotation = res.data.message;
                this.nowCoin = this.quotation[0].name;
                }
            });
        },

    }
};
</script>

<style lang='scss'>
.bg_active{
    background: #f8f8f8;
}
#c2c-box {
  border-top: 1px solid #ddd;
  font-size: 14px;
  > .c2c-l {
    border-right: 1px solid #ddd;
    padding: 30px;
    width: 23%;
    ul{
        background: #dfe8f3;
    }
    li{
        padding: 0 10px;
        justify-content: space-between;
        cursor: pointer;
       
        line-height: 40px;
        &:hover{
            background: #f8f8f8;
        }
        .redColor{
            margin-left: 10px;
        }
    }
  }
  > .c2c-r {
    padding: 10px 30px;
    width: 77%;
    > .top {
      > .top-title {
        font-size: 24px;
        line-height: 2;
        .link-span {
          cursor: pointer;
          &:hover {
            color: #de5959;
          }
        }
      }
      .top-price {
        > div {
          margin-right: 20px;
          // line-height: 1.8;
          > span:last-child {
            font-weight: 600;
            font-size: 16px;
          }
        }
      }
      .inp-items {
        > div:first-child {
          margin-right: 30px;
          border-right: 1px dashed #ddd;
          padding-right: 30px;
        }
        > .inp-item {
          flex: 1;
          > .inp-title {
            border-bottom: 1px solid #ddd;
            font-size: 16px;
            justify-content: space-between;
            line-height: 3;
          }
          > .how {
            line-height: 40px;
            cursor: pointer;
          }
          > .three-inp {
            > .inp-box {
              position: relative;
              height: 42px;
              line-height: 40px;
              padding-left: 112px;
              margin-bottom: 15px;
              > span {
                position: absolute;
                width: 112px;
                text-align: center;
                top: 0;
                left: 0;
                background: #eee;
              }
              > div,
              > input {
                display: block;
                width: 100%;
                line-height: 40px;
                padding: 0 20px;
              }
              > div {
                color: #ca4141;
              }
            }
            > div:nth-child(n + 2) {
              border: 1px solid #ddd;
              > input {
                border-left: 1px solid #ddd;
              }
            }
          }
          .pay-opts {
              >div:nth-child(n+2){
                  margin-left: 20px;
              }
            > div {
            //   padding-right: 20px;
              line-height: 40px;
              > input {
                vertical-align: middle;
              }
            }
          }
          > .btn-in,
          .btn-out {
            margin-top: 20px;
            line-height: 42px;
            text-align: center;
            cursor: pointer;
            color: #fff;
          }
          > .btn-in {
            background: #ca4141;
          }
          > .btn-out {
            background: #25796a;
          }
        }
      }
    }
    > .bot {
      > .bot-title {
        margin: 30px 0 10px;
        font-size: 16px;
        line-height: 40px;
        justify-content: space-between;
      }
      > .list-title {
        height: 40px;
        //   justify-content: space-between;
        line-height: 40px;
        font-weight: 600;
      }
      .list-title,
      ul li {
        justify-content: space-between;
        > div:last-child {
          width: 55px;
          color: #fff;
          margin-left: 70px;
          text-align: center !important;
          cursor: pointer;
        }
        > div:first-child,
        #divPushOrder tr th:first-child {
          width: 6%;
        }
        > div:nth-child(2),
        #divPushOrder tr th:nth-child(2) {
          width: 8%;
        }
        > div:nth-child(3),
        #divPushOrder tr th:nth-child(3) {
          width: 12%;
        }
        > div:nth-child(4),
        #divPushOrder tr th:nth-child(4) {
          width: 12%;
        }
        > div:nth-child(5),
        #divPushOrder tr th:nth-child(5) {
          width: 14%;
        }
        > div:nth-child(6),
        #divPushOrder tr th:nth-child(6) {
          width: 11%;
        }
        > div:nth-child(7),
        #divPushOrder tr th:nth-child(7) {
          width: 9%;
        }
        > div:nth-child(8),
        #divPushOrder tr th:nth-child(8) {
          width: 10%;
        }
        > div:nth-child(9),
        #divPushOrder tr th:nth-child(9) {
          width: 10%;
        }
        div:nth-child(n + 2) {
          text-align: right;
        }
      }
      li {
        padding: 8px 0;
        line-height: 24px;
        border-top: 1px solid #ddd;
        //   justify-content: space-between;
      }
      .ul-out li {
        div:first-child {
          color: #25796a;
        }
        div:last-child {
          background: #25796a;
        }
      }
      .ul-in li {
        div:first-child {
          color: #ca4141;
        }
        >div:last-child{
            background: #ca4141;
        }
      }
    }
  }
}
</style>
