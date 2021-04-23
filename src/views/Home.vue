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
      <van-list v-model="loading" :finished="finished" finished-text="没有更多了" @load="onLoad">
          <van-cell v-for="item in list" :key="item" :title="item" />
      </van-list>
      <div id="footer"> footer </div>
  </div>
</template>

<script>
import navBar from '@/components/NavBar'
import swiper from '@/components/Swiper'
import { getHome } from '../service/home'
import { getUserInfo } from '../service/user'
import { getLocal } from '@/common/js/utils'
import { Toast } from 'vant'
import NavBar from '../components/NavBar.vue'
// import { List } from 'vant';

export default {
  name: 'home',
  components: {
    navBar,
    // List
  },
  data() {
    return {
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
	background-color:#F63;
	height:80px;
}
</style>
