<template>
<view>
<view class="mypoints">
  <view class="top_box" :style="'background:' + colors">
    <view class="left">
      <view class="p1">总积分</view>
      <view class="p2">1273</view>
    </view>
    <view class="right">
      <view @tap="onexchange">
         兑换礼品
      </view> 
    </view>
  </view>
  <view class="jilu">
    <view class="jilu_title">
      <view class="place" :style="'background:' + colors"></view>
      <view class="texts">消费记录</view>
    </view>
    <view class="jilu_box">
      <view v-for="(item, index) in logList" :key="index" class="jilu_list">
        <view class="list_left">
          <text style="color:#333">{{item.name}}</text>
          <text style="color: #999;font-size:24upx">2020-08-025 19：52</text>
        </view>
        <view class="list_right" style="font-size:30upx; color: #10D3EA" v-if="item.type == 0">+{{item.number}}</view>
        <view class="list_right" style="font-size:30upx; color: #EC1818" v-if="item.type == 1">-{{item.number}}</view>
      </view>
      <view class="daodi" v-if="logList.length >= 5">—— 到底啦 ——</view>
      <nodata :colors="colors" title="暂无消费记录" v-if="logList.length == 0"></nodata>
    </view>
  </view>
</view>
<loading v-if="isShow == true"></loading>
</view>
</template>

<script>
var app = getApp();
import loading from "../../commponent/public/loading";
export default {
  data() {
    return {
      logList: [{
        name: '购买商品',
        type: 0,
        number: 18
      }, {
        name: '兑换商品',
        type: 1,
        number: 18
      }, {
        name: '兑换商品',
        type: 1,
        number: 18
      }, {
        name: '兑换商品',
        type: 1,
        number: 18
      }, {
        name: '兑换商品',
        type: 1,
        number: 18
      }, {
        name: '兑换商品',
        type: 1,
        number: 18
      }],
      isShow: true,
      colors: ""
    };
  },

  components: {
    loading
  },
  props: {},

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    this.setData({
      colors: app.globalData.newColor
    });
    uni.setNavigationBarColor({
      backgroundColor: app.globalData.newColor,
      frontColor: '#ffffff'
    });
    setTimeout(() => {
      this.setData({
        isShow: false
      });
    }, 600);
  },

  /**
   * 生命周期函数--监听页面初次渲染完成
   */
  onReady: function () {},

  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function () {},

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () {},

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () {},

  /**
   * 页面相关事件处理函数--监听用户下拉动作
   */
  onPullDownRefresh: function () {},

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {},

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage: function () {},
  methods: {
    onexchange() {
      uni.navigateTo({
        url: '/pages/views/user/exchange/exchange'
      });
    }

  }
};
</script>
<style lang="scss" scoped>
.mypoints{
  padding-bottom: 20upx;
}
.top_box{
  width: 100vw;
  height: 28vw;
  display: flex;
  justify-content: space-between;
  padding: 0 4%;
  align-items: center;
}
.top_box .left .p1{
  font-size: 26upx;
  color: #fff;
  line-height: 60upx;
}
.top_box .left .p2{
  font-size: 36upx;
  color: #fff;
  line-height: 80upx;
}
.top_box .right{
  display: flex;
}
.top_box .right view{
  height: 60upx;
  line-height: 60upx;
  padding:0 20upx;
  color: #fff;
  border: 1upx solid #fff;
  border-radius: 40upx;
  margin-top: 60upx;
}
.jilu{
  margin: 0 4%;
  padding: 20upx;
  box-shadow: 0upx 0upx 10upx #aaa;
  border-radius: 10upx;
  margin-top: 20upx;
}
.jilu_title{
  display: flex;
  height: 80upx;
  line-height: 80upx;
  align-items: center;
  border-bottom: 1upx solid #eee;
}
.jilu_title .place{
  width: 10upx;
  height: 50upx;
  background-color: #3E7E8B;
  margin-right: 20upx;
  border-radius: 15upx;
}
.jilu_title .texts{
  font-size: 30upx;
  color: #333;
  font-weight: 500;
}
.jilu_box{
  min-height: 220upx;
  padding-bottom: 20upx;
  
}
.jilu_box .jilu_list{
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
  padding: 20upx 0;
  border-bottom: 1upx solid #eee;
}
.jilu_list text{
  display: block;
  height: 50upx;
  line-height: 50upx;
}
.daodi{
  text-align: center;
  color: #ccc;
  font-size: 24upx;
  margin-top: 20upx;
}
</style>