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
                            <div class="redColor">（必须本人支付)</div>
                            <router-link tag="div" to="/c2c">《交易须知》</router-link>
                        </div>
                        <div class="btn-in bgRed" @click="bui_in">买入（CNY→{{currency_name}}）</div>
                    </div>
                    <div class="inp-item">
                        <div class="inp-title flex">
                            <div>
                                <span>卖出{{currency_name}}</span>
                                <span class="ft14" style="color:#666">({{currency_name}}余额)</span>
                                </div>
                            <div style='font-size:14px;color:#666'>
                                <router-link to="/c2c" style="margin-right:10px">{{currency_name}}充值</router-link>
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
                                <input type="text" v-model="num01">
                            </div>
                            <div class="inp-box">
                                <span>金额CNY</span>
                                <input type="text" v-model="price01">
                            </div>
                             <div class="inp-box">
                                <span>姓名</span>
                                <input type="text" v-model="user_name01">
                            </div>
                            <div class="inp-box">
                                <span>详细内容</span>
                                <input type="text" v-model="content01" />
                            </div>
                        </div>
                        <div class="pay-opts flex">
                            <div>支付方式</div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay01" value="支付宝" v-model="pay01">
                                    支付宝
                                </label>
                            </div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay01" value="微信" v-model="pay01">
                                    微信
                                </label>
                            </div>
                            <div>
                    
                                <label >
                                    <input type="radio" name="pay01" value="银行卡" v-model="pay01">
                                    银行转账
                                </label>
                            </div>
                            
                            <div class="redColor">（必须本人支付)</div>
                            <router-link tag="div" to="/c2c">《交易须知》</router-link>
                        </div>
                        <div class="btn-out" @click="sell_out">卖出（{{currency_name}}→CNY）</div>

                    </div>
                    
                </div>
            </div>
            <div class="bot">
                
                <div class="bot-title flex">
                    <div>
                        <span @click="nowList ='listIn'" :class="{'active':nowList == 'listIn'}">c2c</span>
                        <span @click="nowList =  'myAdd'" :class="{'active':nowList == 'myAdd'}">我发布的c2c</span>
                        <span @click="nowList = 'myBuySell'" :class="{'active':nowList == 'myBuySell'}">我交易的c2c</span>
                    
                    </div>
                    <div class="flex" @click="showList = !showList">
                        <div :class="[{'switch-on':!showList},{'switch':showList}]"><div></div></div>
                        <span class="ft14">显示市场挂单</span>
                    </div>
                </div>
                <div class="list-title flex" v-if="showList">
                    <div>类型</div>
                    <div>价格(CNY)</div>
                    <div>数量(USDT)</div>
                    <div>总计(CNY)</div>
                    <!-- <div>交易限额(USDT)</div> -->
                    <div>商家</div>
                    <!-- <div>成交单数</div> -->
                    <!-- <div>平均用时</div> -->
                    <div>付款方式</div>
                    <div style="visibility: hidden;">一一24234234</div>
                </div>
                <div class="ul-box" v-if="nowList == 'listIn'">
                    <ul class="ul-out" v-if="showList&&listOut.list.length">
                        <li v-for="(item,index) in listOut.list" :key="index" class="flex">
                            <div style="color:#25796a">卖出</div>
                            <div>{{item.price}}</div>
                            <div>{{item.number}}</div>
                            <div>{{(item.number*item.price-0).toFixed(2)}}</div>
                            <!-- <div></div> -->
                            <div>{{item.name}}</div>
                            <!-- <div></div> -->
                            <!-- <div></div> -->
                            <div>{{item.pay_mode}}</div>
                            <div @click="buySell(item.id,'buy')">买入</div>
                        </li>
                        <!-- <li class="flex">
                            
                        </li> -->
                        
                    </ul>
                    <div class="more"  v-if="listOut.length&&listOut.hasMore" @click="getList(1)">加载更多</div>
                    <ul class="ul-in" v-if="showList&&listIn.list.length">
                        <li v-for="(item,index) in listIn.list" :key="index" class="flex">
                            <div>买入</div>
                            <div>{{item.price}}</div>
                            <div>{{item.number}}</div>
                            <div>{{(item.number*item.price-0).toFixed(2)}}</div>
                            <!-- <div></div> -->
                            <div>{{item.name}}</div>
                            <!-- <div></div> -->
                            <!-- <div></div> -->
                            <div>{{item.pay_mode}}</div>
                            <div @click="buySell(item.id,'sell')">卖出</div>
                        </li>
                        
                    </ul>
                    <div class="more"  v-if="listIn.list.length&&listIn.hasMore" @click="getList(0)">加载更多</div>

                </div>
                <div class="ul-box" v-if="nowList == 'myAdd'">
                    <ul class="ul-out" v-if="showList&&myAdd.list.length">
                        <li v-for="(item,index) in myAdd.list" :key="index" class="flex">
                            <div style="color:#25796a">卖出</div>
                            <div>{{item.price}}</div>
                            <div>{{item.number}}</div>
                            <div>{{(item.number*item.price-0).toFixed(2)}}</div>
                            <!-- <div></div> -->
                            <div>{{item.name}}</div>
                            <!-- <div></div> -->
                            <!-- <div></div> -->
                            <div>{{item.pay_mode}}</div>
                            <div @click="cancelComplete('cancel',item.id)">取消发布</div>
                        </li>
                        <!-- <li class="flex">
                            
                        </li> -->
                        
                    </ul>
                    <div class="more"  v-if="myAdd.length&&myAdd.hasMore" @click="getMy('myAdd')">加载更多</div>
                    
                </div>
                <div class="ul-box" v-if="nowList == 'myBuySell'">
                    <ul class="ul-out" v-if="showList&&myBuySell.list.length">
                        <li v-for="(item,index) in myAdd.list" :key="index" class="flex">
                            <div style="color:#25796a">卖出</div>
                            <div>{{item.price}}</div>
                            <div>{{item.number}}</div>
                            <div>{{(item.number*item.price-0).toFixed(2)}}</div>
                            <!-- <div></div> -->
                            <div>{{item.name}}</div>
                            <!-- <div></div> -->
                            <!-- <div></div> -->
                            <div>{{item.pay_mode}}</div>
                            <div @click="cancelComplete('complete',item.id)">确认</div>
                        </li>
                        <!-- <li class="flex">
                            
                        </li> -->
                        
                    </ul>
                    <div class="more"  v-if="myBuySell.length&&myBuySell.hasMore" @click="getMy('myBuySell')">加载更多</div>
                    

                </div>
                
            </div>
        </div>
    </div>
</template>

<script>
export default {
  data() {
    return {
      token: "",
      nowList: "listIn",
      listIn: { page: 1, list: [], hasMore: true },
      listOut: { page: 1, list: [], hasMore: true },
      myAdd: { page: 1, list: [], hasMore: true },
      myBuySell: { page: 1, list: [], hasMore: true },
      active: 0,
      currency_list: [],
      currency_name: "",
      id: "",
      price: "",
      num: "",
      pay: "",
      user_name: "",
      content: "",
      price01: "",
      num01: "",
      pay01: "",
      user_name01: "",
      content01: "",
      currency_list: [],
      currency_name: "",
      showList: true
    };
  },
  created() {
    this.token = window.localStorage.getItem("token") || "";
    this.get_currency();
    this.getList(1);
    this.getList(0);
    this.getMy("myAdd");
    this.getMy("myBuySell");
  },
  methods: {
    // 获取币种列表
    get_currency() {
      this.$http({
        url: "/api/currency/list",
        method: "get",
        headers: { Authorization: this.token }
      }).then(res => {
        //console.log(res);
        if (res.data.type == "ok") {
          this.currency_list = res.data.message.legal;
          this.currency_name = res.data.message.legal[0].name;
          this.id = res.data.message.legal[0].id;
        }
      });
    },
    //选择币种
    currency_click(id, name, index) {
      this.currency_name = name;
      this.active = index;
      this.id = id;
    },
    // 获取c2clist
    getList(type) {
      let page = 1;
      page = type == 1 ? this.listOut.page : this.listIn.page;
      //console.log(type);

      this.$http({
        url: "/api/c2c/list?type=" + type + "&page=" + page,

        method: "get",
        headers: { Authorization: this.token }
      }).then(res => {
        if (res.data.type == "ok") {
          let list = res.data.message.list;
          //console.log(list);

          if (list.length != 0) {
            // console.log(list);

            if (type == 1) {
              this.listOut.list = this.listOut.list.concat(list);

              this.listOut.page += 1;
              console.log(this.listOut);
            } else {
              console.log(this.listIn.list);

              this.listIn.list = this.listIn.list.concat(list);
              // console.log( this.listIn.list);

              // console.log(this.listIn);

              this.listIn.page += 1;
              // console.log([].concat(list));
            }
          } else {
            type == 1
              ? (this.listOut.hasMore = false)
              : (this.listIn.hasMore = false);
          }
        }
      });
    },
    // c2c列表买入卖出
    buySell(id, type) {
      this.$http({
        url: "/api/c2c/" + type,
        method: "post",
        data: { id: id },
        headers: { Authorization: this.token }
      }).then(res => {
        layer.msg(res.data.message);
        if (res.data.type == "ok") {
          console.log(res.data);
        }
      });
    },
    getMy(type) {
      let t = type == "myAdd" ? "my_add" : "my_transaction";
      this.$http({
        url: "/api/c2c/" + t,
        data: { page: this[type].page },
        headers: { Authorization: this.token }
      }).then(res => {
        if (res.data.type == "ok") {
          if (res.data.message.length == 0) {
            this[type]["hasMore"] = false;
          } else {
            let list = res.data.message;
            this[type]["list"] = this[type]["list"].concat(list);
            this[type]["page"] += 1;
          }
        }
      });
    },
    cancelComplete(type, id) {
      this.$http({
        url: "/api/c2c/" + type,
        method: "post",
        data: { id: id },
        headers: { Authorization: this.token }
      }).then(res => {
        console.log(res);

        layer.msg(res.message);
      });
    },

    //获取我发布的
    getMyAdd() {},
    //添加买入
    bui_in() {
      this.$http({
        url: "/api/c2c/add",
        method: "post",
        data: {
          // price:this.price,
          // number:this.num,
          // name:this.user_name,
          // pay_mode:'微信',
          // content:this.content,
          // token:this.currency_name
          price: this.price,
          number: this.num,
          name: this.user_name,
          pay_mode: this.pay,
          content: this.content,
          token: this.currency_name,
          type: 0
        },
        headers: { Authorization: this.token }
      }).then(res => {
        console.log(res);
        layer.msg(res.data.message);
        this.price = "";
        this.num = "";
        this.user_name = "";
        this.pay = "";
        this.content = "";
      });
    },
    //添加卖出
    sell_out() {
      this.$http({
        url: "/api/c2c/add",
        method: "post",
        data: {
          price: this.price01,
          number: this.num01,
          name: this.user_name01,
          pay_mode: this.pay01,
          content: this.content01,
          token: this.currency_name,
          type: 1
        },
        headers: { Authorization: this.token }
      })
        .then(res => {
          console.log(res);
          layer.msg(res.data.message);
          this.price = "";
          this.num = "";
          this.user_name = "";
          this.pay = "";
          this.content = "";
        })
        .catch(res => {
          layer.msg(res.data.message);
        });
    }
  }
};
</script>

<style lang='scss'>
.more {
  color: #ca4141;
  text-align: center;
  cursor: pointer;
}
.bg_active {
  background: #f8f8f8;
}
#c2c-box {
  border-top: 1px solid #ddd;
  font-size: 14px;
  > .c2c-l {
    border-right: 1px solid #ddd;
    padding: 30px;
    width: 23%;
    ul {
      background: #dfe8f3;
    }
    li {
      padding: 0 10px;
      justify-content: space-between;
      cursor: pointer;

      line-height: 40px;
      &:hover {
        background: #f8f8f8;
      }
      .redColor {
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
            flex-wrap: wrap;
            > div:nth-child(n + 2) {
              margin-left: 15px;
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
        border-bottom: 1px solid #ccc;
        font-size: 16px;
        line-height: 40px;
        justify-content: space-between;
        align-items: center;
        >div:first-child{
                cursor: pointer;
            span{
                font-weight: 600;
                line-height: 40px;
                margin-right: 20px;
            }
            .active{
                color: #ca4141;
                border: none;
            }
        }
        > .flex {
          height: 17px;
          line-height: 15px;
          cursor: pointer;
          > div {
            margin-right: 10px;
            border: 1px solid #ccc;
            transition: all 0.3s;
            width: 32px;
            border-radius: 7.5px;
            div {
              width: 15px;
              height: 15px;
              border-radius: 50%;
              background: #fff;
            }
          }
          .switch-on {
            padding-left: 0;
            background: #ccc;
          }
          .switch {
            transition: all 0.3s;
            background: #1cb69b;
            padding-left: 15px;
          }
        }
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

        > div {
          flex: 1;
          text-align: center;
        }
        > div:first-child {
          flex: 0.5;
          text-align: left;
        }
        > div:nth-child(n + 2) {
          flex: 1;
        }
        > div:last-child {
          flex: none;
          padding: 0 10px;
          min-width: 55px;
          max-width: 80px;
          color: #fff;
          //   margin-left: 70px;
          text-align: center !important;
          cursor: pointer;
        }
        // > div:first-child,
        // #divPushOrder tr th:first-child {
        //   width: 6%;
        // }
        // > div:nth-child(2),
        // #divPushOrder tr th:nth-child(2) {
        //   width: 8%;
        // }
        // > div:nth-child(3),
        // #divPushOrder tr th:nth-child(3) {
        //   width: 12%;
        // }
        // > div:nth-child(4),
        // #divPushOrder tr th:nth-child(4) {
        //   width: 12%;
        // }
        // > div:nth-child(5),
        // #divPushOrder tr th:nth-child(5) {
        //   width: 14%;
        // }
        // > div:nth-child(6),
        // #divPushOrder tr th:nth-child(6) {
        //   width: 11%;
        // }
        // > div:nth-child(7),
        // #divPushOrder tr th:nth-child(7) {
        //   width: 9%;
        // }
        // > div:nth-child(8),
        // #divPushOrder tr th:nth-child(8) {
        //   width: 10%;
        // }
        // > div:nth-child(9),
        // #divPushOrder tr th:nth-child(9) {
        //   width: 10%;
        // }
        // div:nth-child(n + 2) {
        //   text-align: right;
        // }
      }
      li {
        padding: 8px 5px;
        line-height: 24px;
        border-top: 1px solid #ddd;
        &:hover {
          background: #f8f8f8;
        }
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
        > div:last-child {
          background: #ca4141;
        }
      }
    }
  }
}
</style>
