<!--
 * 严肃声明：
 * 开源版本请务必保留此注释头信息，若删除我方将保留所有法律责任追究！
 * 本系统已申请软件著作权，受国家版权局知识产权以及国家计算机软件著作权保护！
 * 可正常分享和学习源码，不得用于违法犯罪活动，违者必究！
 * Copyright (c) 2020 陈尼克 all rights reserved.
 * 版权所有，侵权必究！
 *
-->

<template>
  <div id="box">
    <nav-bar></nav-bar>
      <div id="banner">
        <img src="../assets/gzzc.jpg" alt="广州资产" width="100%" height="100%"/>
      </div>
      <div class="category-list">
          <div v-for="item in categoryList" v-bind:key="item.categoryId">
            <img :src="item.imgUrl">
            <span>{{item.name}}</span>
          </div>
      </div>
      <br/>
      <div id="main">
        <!--左边区域-->
        <div id="left">
           <div>业务规模</div>
           <div style="color:blue;font-size:15px;">9999999999.00万</div>
           <br/>
           <div>
              <div><span>20.0</span><span style="float:right;">20.0</span> </div>
              <div><span>当月</span><span style="float:right;">当季</span> </div>
            </div>
          </div>
        <div id="right">
           <div>业务规模</div>
           <div style="color:blue;font-size:15px;">9999999999.00万</div>
           <br/>
           <div>
              <div><span>20.0</span><span style="float:right;">20.0</span> </div>
              <div><span>当月</span><span style="float:right;">当季</span> </div>
            </div>
          </div>      
      </div>
      <div id="myChart" :style="{width: '300px', height: '300px' ,margin_top:'30px'}"></div>
      <!-- Table goes in the document BODY -->
      <div>
        <table class="table1">
          <thead>
            <tr>
              <th></th>
              <th scope="col" abbr="Starter">本月末</th>
              <th scope="col" abbr="Medium">比上月</th>
              <th scope="col" abbr="Business">比上季</th>
            </tr>
          </thead>
          <!-- <tfoot>
            <tr>
              <th scope="row">Price per month</th>
              <td>$ 2.90</td>
              <td>$ 5.90</td>
              <td>$ 9.90</td>
              <td>$ 14.90</td>
            </tr>
          </tfoot> -->
          <tbody>
            <tr>
              <th scope="row">Storage Space</th>
              <td>512 MB</td>
              <td>1 GB</td>
              <td>2 GB</td>
            </tr>
            <tr>
              <th scope="row">Bandwidth</th>
              <td>50 GB</td>
              <td>100 GB</td>
              <td>150 GB</td>
            </tr>
            <tr>
              <th scope="row">MySQL Databases</th>
              <td>Unlimited</td>
              <td>Unlimited</td>
              <td>Unlimited</td>
            </tr>
          </tbody>
        </table>
      </div>
      <div>
        <h3>账务数据</h3>
        <select v-model="queryDate">
              <option :key="x.text" v-for="x in option1">{{x.text}}</option>
        </select>
      </div>
      <div id="finance-chart-1" :style="{width: '50%', height: '100%' ,margin_top:'10'}"></div>
      <div id="finance-chart-2" :style="{width: '50%', height: '100%' ,margin_top:'10'}"></div>
      <div id="finance-chart-3" :style="{width: '50%', height: '100%' ,margin_top:'10'}"></div>
      <div id="finance-chart-4" :style="{width: '50%', height: '100%' ,margin_top:'10'}"></div>
      <div id="footer"></div>
  </div>
</template>

<script>
import navBar from '@/components/NavBar'
import swiper from '@/components/Swiper'
import { getHome } from '../service/home'
import { getUserInfo } from '../service/user'
import { getLocal } from '@/common/js/utils'
import NavBar from '../components/NavBar.vue'

export default {
  name: 'home',
  components: {
    navBar
  },
  data() {
    return {
      queryDate:'',
      option1: [
        { text: '2021-1', value: 0 },
        { text: '2021-2', value: 1 },
        { text: '2021-3', value: 2 },
      ],
      list: [],
      loading: false,
      finished: false,
      swiperList: [],
      isLogin: false,
      headerScroll: false,
      hots: [],
      newGoodses: [],
      recommends: [],
      categoryList: [
          {
            name: '新蜂超市',
            imgUrl: '//s.weituibao.com/1583585285461/cs.png',
            categoryId: 100001
          }, {
            name: '新蜂服饰',
            imgUrl: '//s.weituibao.com/1583585285468/fs.png',
            categoryId: 100003
          }, {
            name: '全球购',
            imgUrl: '//s.weituibao.com/1583585285470/qq.png',
            categoryId: 100002
          }, {
            name: '新蜂生鲜',
            imgUrl: '//s.weituibao.com/1583585285472/sx.png',
            categoryId: 100004
          }, {
            name: '新蜂到家',
            imgUrl: '//s.weituibao.com/1583585285467/dj.png',
            categoryId: 100005
          }, {
            name: '充值缴费',
            imgUrl: '//s.weituibao.com/1583585285465/cz.png',
            categoryId: 100006
          }, {
            name: '9.9元拼',
            imgUrl: '//s.weituibao.com/1583585285469/pt.png',
            categoryId: 100007
          }, {
            name: '领劵',
            imgUrl: '//s.weituibao.com/1583585285468/juan.png',
            categoryId: 100008
          }, {
            name: '省钱',
            imgUrl: '//s.weituibao.com/1583585285471/sq.png',
            categoryId: 100009
          }, {
            name: '全部',
            imgUrl: '//s.weituibao.com/1583585285470/qb.png',
            categoryId: 100010
          } 
      ],
    }
  },

  async mounted() {
    const token = getLocal('token')
    if (token) {
      this.isLogin = true
    }
    // 画图
    this.drawLine();
  },
  methods: {
    onLoad() {
      // 异步更新数据
      // setTimeout 仅做示例，真实场景中一般为 ajax 请求
      setTimeout(() => {
        let title=['','本月末','比上月','比上季']
        this.list.push(title);
        let data = [          
              ['资产管理余额','本月末','比上月','比上季'],
              ['投入资金余额','本月末','比上月','比上季'],
              ['因收资产余额','本月末','比上月','比上季']
            ]

        for (let i = 0; i < 4; i++) {
          this.list.push(data[i]);
        }
        // 加载状态结束
        this.loading = false;
        // 数据全部加载完成
        // if (this.list.length >= 40) {
        this.finished = true;
        // }
      }, 1000);
  },
    drawLine(){
          // 基于准备好的dom，初始化echarts实例
          let myChart = this.$echarts.init(document.getElementById('myChart'))
          // 绘制图表
          myChart.setOption({
              title: { text: '在Vue中使用echarts' },
              tooltip: {},
              xAxis: {
                  data: ["衬衫","羊毛衫","雪纺衫","裤子","高跟鞋","袜子"]
              },
              yAxis: {},
              series: [{
                  name: '销量',
                  type: 'bar',
                  data: [5, 20, 36, 10, 10, 20]
              }]
          });


            // 基于准备好的dom，初始化echarts实例
          let myChart1 = this.$echarts.init(document.getElementById('finance-chart-1'))
          // 绘制图表
          myChart1.setOption({
                 series: [{
                    type: 'gauge',
                    startAngle: 180,
                    endAngle: 0,
                    min: 0,
                    max: 1,
                    splitNumber: 8,
                    axisLine: {
                        lineStyle: {
                            width: 6,
                            color: [
                                [0.25, '#FF6E76'],
                                [0.5, '#FDDD60'],
                                [0.75, '#58D9F9'],
                                [1, '#7CFFB2']
                            ]
                        }
                    },
                    pointer: {
                        icon: 'path://M12.8,0.7l12,40.1H0.7L12.8,0.7z',
                        length: '20%',
                        width: 10,
                        offsetCenter: [0, '-80%'],
                        itemStyle: {
                            color: 'red'
                        }
                    },
                    axisTick: {
                        length: 12,
                        lineStyle: {
                            color: 'auto',
                            width: 2
                        }
                    },
                    splitLine: {
                        length: 20,
                        lineStyle: {
                            color: 'auto',
                            width: 5
                        }
                    },
                    axisLabel: {
                        color: '#464646',
                        fontSize: 12,
                        distance: -40,
                        formatter: function (value) {
                            if (value === 0.875) {
                                return '优';
                            }
                            else if (value === 0.625) {
                                return '中';
                            }
                            else if (value === 0.375) {
                                return '良';
                            }
                            else if (value === 0.125) {
                                return '差';
                            }
                        }
                    },
                    title: {
                        offsetCenter: [0, '-20%'],
                        fontSize: 12
                    },
                    detail: {
                        fontSize: 12,
                        offsetCenter: [0, '0%'],
                        valueAnimation: true,
                        formatter: function (value) {
                            return Math.round(value * 100) + '分';
                        },
                        color: 'auto'
                    },
                    data: [{
                        value: 0.70,
                        fontSize: 12,
                        name: '成绩评定'
                    }]
                }]
          });


 // 基于准备好的dom，初始化echarts实例
          let myChart2= this.$echarts.init(document.getElementById('finance-chart-2'))
          // 绘制图表
          myChart2.setOption({
                 series: [{
                      type: 'gauge',
                      progress: {
                          show: true,
                          width: 18
                      },
                      axisLine: {
                          lineStyle: {
                              width: 18
                          }
                      },
                      axisTick: {
                          show: false
                      },
                      splitLine: {
                          length: 15,
                          lineStyle: {
                              width: 2,
                              color: '#999'
                          }
                      },
                      axisLabel: {
                          distance: 25,
                          color: '#999',
                          fontSize: 20
                      },
                      anchor: {
                          show: true,
                          showAbove: true,
                          size: 25,
                          itemStyle: {
                              borderWidth: 10
                          }
                      },
                      title: {
                          show: false
                      },
                      detail: {
                          valueAnimation: true,
                          fontSize: 80,
                          offsetCenter: [0, '70%']
                      },
                      data: [{
                          value: 70
                      }]
                  }]})

  
 // 基于准备好的dom，初始化echarts实例
          let myChart3 = this.$echarts.init(document.getElementById('finance-chart-3'))
          // 绘制图表
          myChart3.setOption({
                 series: [{
                      type: 'gauge',
                      progress: {
                          show: true,
                          width: 18
                      },
                      axisLine: {
                          lineStyle: {
                              width: 18
                          }
                      },
                      axisTick: {
                          show: false
                      },
                      splitLine: {
                          length: 15,
                          lineStyle: {
                              width: 2,
                              color: '#999'
                          }
                      },
                      axisLabel: {
                          distance: 25,
                          color: '#999',
                          fontSize: 20
                      },
                      anchor: {
                          show: true,
                          showAbove: true,
                          size: 25,
                          itemStyle: {
                              borderWidth: 10
                          }
                      },
                      title: {
                          show: false
                      },
                      detail: {
                          valueAnimation: true,
                          fontSize: 80,
                          offsetCenter: [0, '70%']
                      },
                      data: [{
                          value: 70
                      }]
                  }]})


            // 基于准备好的dom，初始化echarts实例
          let myChart4 = this.$echarts.init(document.getElementById('finance-chart-4'))
          // 绘制图表
          myChart4.setOption({
                 series: [{
                    type: 'gauge',
                    startAngle: 180,
                    endAngle: 0,
                    min: 0,
                    max: 1,
                    splitNumber: 8,
                    axisLine: {
                        lineStyle: {
                            width: 6,
                            color: [
                                [0.25, '#FF6E76'],
                                [0.5, '#FDDD60'],
                                [0.75, '#58D9F9'],
                                [1, '#7CFFB2']
                            ]
                        }
                    },
                    pointer: {
                        icon: 'path://M12.8,0.7l12,40.1H0.7L12.8,0.7z',
                        length: '20%',
                        width: 10,
                        offsetCenter: [0, '-10%'],
                        itemStyle: {
                            color: 'red'
                        }
                    },
                    axisTick: {
                        length: 12,
                        lineStyle: {
                            color: 'auto',
                            width: 2
                        }
                    },
                    splitLine: {
                        length: 20,
                        lineStyle: {
                            color: 'auto',
                            width: 5
                        }
                    },
                    axisLabel: {
                        color: '#464646',
                        fontSize: 12,
                        distance: -40,
                        formatter: function (value) {
                            if (value === 0.875) {
                                return '优';
                            }
                            else if (value === 0.625) {
                                return '中';
                            }
                            else if (value === 0.375) {
                                return '良';
                            }
                            else if (value === 0.125) {
                                return '差';
                            }
                        }
                    },
                    title: {
                        offsetCenter: [0, '-20%'],
                        fontSize: 12
                    },
                    detail: {
                        fontSize: 12,
                        offsetCenter: [0, '0%'],
                        valueAnimation: true,
                        formatter: function (value) {
                            return Math.round(value * 100) + '分';
                        },
                        color: 'auto'
                    },
                    data: [{
                        value: 0.70,
                        fontSize: 12,
                        name: '成绩评定'
                    }]
                }]
          });

      },

  }
}
</script>
<style lang="less" scoped >
@import '../common/style/mixin';
#box{
	/* background-color: #e59a1d; */
	width:100%;
	height:100%;
	/*margin:0 auto;*/
	margin:0 auto 0 auto;/*上 右 下 左*/
}
/*放图片，设置高度和图片一样高*/
#banner{
	/* background-color:#e59a1d; */
	height:130px;
}
.category-list{
  display: flex;
  flex-shrink: 0;
  flex-wrap: wrap;
  width: 100%;
  padding-bottom: 13px;
    div {
      display: flex;
      flex-direction: column;
      width: 20%;
      text-align: center;
      img {
        .wh(40px, 40px);
        margin: 13px auto 8px auto;
      }
    }
}
#main{
	// background-color:#e59a1d;
  width: 100%;
	height:120px;
  margin-bottom: 30px;
}
#left{
	// background-color:#e59a1d;
  background: -webkit-linear-gradient(top,white,rgb(211, 230, 236));
	height:100%;
	width:40%;
	float:left;/*可以使div横向排排坐*/
  border: 10px;
  border: 10px solid transparent;
  border-radius: 5px 5px 5px 5px;
  -moz-border-top-colors:#a0a #909 #808 #707 #606 #505 #404 #303;
  -moz-border-right-colors:#a0a #909 #808 #707 #606 #505 #404 #303;
  -moz-border-bottom-colors:#a0a #909 #808 #707 #606 #505 #404 #303;
  -moz-border-left-colors:#a0a #909 #808 #707 #606 #505 #404 #303;
}
#right{
	// background-color:#e59a1d;
  background: -webkit-linear-gradient(top,white,lightblue);
	height:100%;
	width:40%;
	float:right;/*可以使div横向排排坐*/
  border: 10px solid transparent;
  border-radius: 5px 5px 5px 5px;
  -moz-border-top-colors:#a0a #909 #808 #707 #606 #505 #404 #303;
  -moz-border-right-colors:#a0a #909 #808 #707 #606 #505 #404 #303;
  -moz-border-bottom-colors:#a0a #909 #808 #707 #606 #505 #404 #303;
  -moz-border-left-colors:#a0a #909 #808 #707 #606 #505 #404 #303;
}
 
#footer{
	background-color:rgb(160, 217, 243);
	height:80px;
}

#finace-data{
      width: 100%;
      // margin: 10px;
      height: 30px;
      background: linear-gradient(90deg, @primary, #51c7c7);
      box-shadow: 0 2px 5px #269090;
      border-radius: 6px;
      margin-top: 50px;
    }
table.table1{
    font-family: "Trebuchet MS", sans-serif;
    font-size: 12px;
    font-weight: bold;
    line-height: 1.4em;
    font-style: normal;
    border-collapse:separate;
    width: 100%;
}
.table1 thead th{
    padding:2px;
    color:rgb(15, 14, 14);
    text-shadow:1px 1px 1px#f4f7ef;
    border:1px solid #f4f7ef;
    border-bottom:1px solid #f4f7ef;
    background-color:#f4f7ef;
    background:-webkit-gradient(
        linear,
        left bottom,
        left top,
        color-stop(0.02, #f4f7ef;),
        color-stop(0.51, #f4f7ef;),
        color-stop(0.87,#f4f7ef;)
        );
    background: -moz-linear-gradient(
        center bottom,
        #f4f7ef; 2%,
       #f4f7ef; 51%,
       #f4f7ef; 87%
        );
    -webkit-border-top-left-radius:2px;
    -webkit-border-top-right-radius:2px;
    -moz-border-radius:2px 2px 0px 0px;
    border-top-left-radius:2px;
    border-top-right-radius:2px;
}
.table1 thead th:empty{
    background:transparent;
    border:none;
}
.table1 tbody th{
    color:rgb(15, 14, 14);
    text-shadow:1px 1px 1px#f4f7ef;
    background-color:#f4f7ef;
    border:1px solid#f4f7ef;
    border-right:2px solid#f4f7ef;;
    padding:0px 5px;
    background:-webkit-gradient(
        linear,
        left bottom,
        right top,
        color-stop(0.02, #f4f7ef;),
        color-stop(0.51, #f4f7ef;),
        color-stop(0.87, #f4f7ef;)
        );
    background: -moz-linear-gradient(
        left bottom,
         #f4f7ef; 2%,
        #f4f7ef; 51%,
       #f4f7ef; 87%
        );
    -moz-border-radius:2px 0px 0px 2px;
    -webkit-border-top-left-radius:2px;
    -webkit-border-bottom-left-radius:2px;
    border-top-left-radius:2px;
    border-bottom-left-radius:2px;
}
.table1 tfoot td{
    color: #f4f7ef;
    font-size:32px;
    text-align:center;
    padding:10px 0px;
    text-shadow:1px 1px 1px #444;
}
.table1 tfoot th{
    color:#666;
}
.table1 tbody td{
    padding:10px;
    text-align:center;
    background-color:#DEF3CA;
    border: 2px solid #E7EFE0;
    -moz-border-radius:2px;
    -webkit-border-radius:2px;
    border-radius:2px;
    color:#666;
    text-shadow:1px 1px 1px #fff;
}
// .table1 tbody span.check::before{
//     content : url(../images/check0.png)
// }
</style>
