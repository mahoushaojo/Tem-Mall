<template>
<view class="exchange" :style="'background: linear-gradient(' + colors + ', ' + colors + ',' + (colors+'86') + ',' + (colors+'01') + ')'">
  <view class="ewm_box">
    <view class="titles">
      核销二维码
    </view>
    <view class="ewm_img">
      <image src="../../../../static/images/ewm.png"></image>
    </view>
    <view class="jiefen">我的剩余积分：<text :style="'color:' + colors">1120</text></view>
  </view>
</view>
</template>

<script>
var app = getApp();

export default {
  data() {
    return {
      colors: ""
    };
  },

  components: {},
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
  methods: {}
};
</script>
<style scoped lang="scss">
.exchange{
  position: fixed;
  width: 100%;
  height: 100%;
  bottom: 0;
  top: 0;
  padding: 20upx 4%;
}
.ewm_box{
  width: 100%;
  height: 100vw;
  background-color: #fff;
  margin-top: 140upx;
  border-radius: 10upx;
  overflow: hidden;
}
.ewm_box .titles{
  height: 80upx;
  line-height: 80upx;
  padding: 0 30upx;
  background-color: #E8E8E8;
  font-size: 28upx;
  font-weight: bold;
}
.ewm_img{
  margin-top: 50upx;
}
.ewm_img image{
  width: 400upx;
  height: 400upx;
  display: block;
  margin: 0 auto;
}
.jiefen{
  height: 100upx;
  line-height: 100upx;
  text-align: center;
  font-size: 28upx;
  margin-top: 100upx;
  border-top: 1upx solid #eee;
  font-weight: bold;
}
</style>