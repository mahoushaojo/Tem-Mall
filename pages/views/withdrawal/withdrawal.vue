<template>
<view class="withdrawal">
  <view class="withdrawl_top">
    <image src="/static/images/user/qb.png"></image>
    <view class="money">56.00</view>
    <view class="tips">(我的账户余额)</view>
  </view>
  <view class="minmoney">
    当前最小提现金额为150元
  </view>
  <view class="t_input">
    <view class="title">提现金额</view>
    <view class="inputs">
      <input placeholder="请输入提现金额" placeholder-style="font-size: 28upx; color: #999"></input>
    </view>
    <view class="all_btn" :style="'color:' + colors">全部提现</view>
  </view>
  <view class="jilu" @tap="jumprecord">
    查看提现记录>>
  </view>
  <view class="addclerk" :style="'background:' + colors" @tap="openEwm">
    确认提现
  </view>
</view>
</template>

<script>
var app = getApp();
export default {
  data() {
    return {
      colors: ''
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
    jumprecord() {
      uni.navigateTo({
        url: '/pages/views/withdrawal/withdrawalrecord'
      });
    }

  }
};
</script>
<style lang="scss" scoped>
page {
  background-color: #F5F5FA;
}

.withdrawl_top {
  height: 408upx;
  width: 100%;
  background: rgba(255, 255, 255, 1);
  overflow: hidden;
}

.withdrawl_top image {
  width: 220upx;
  height: 190upx;
  display: block;
  margin: 0 auto;
  margin-top: 60upx;
}

.withdrawl_top .money {
  text-align: center;
  height: 60upx;
  line-height: 60upx;
  font-size: 50upx;
  color: #FF4643;
  margin-top: 20upx;
}

.withdrawl_top .tips {
  text-align: center;
  font-size: 24upx;
  color: #7C7C7C;
  line-height: 50upx;
}

.minmoney {
  padding: 0 4%;
  height: 80upx;
  line-height: 80upx;
  font-size: 26upx;
  font-family: Source Han Sans CN;
  font-weight: 400;
  color: rgba(124, 124, 124, 1);
  opacity: 0.6;
}
.t_input{
  padding: 20upx 4%;
  background-color: #fff;
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.t_input .title{
  height: 80upx;
  line-height: 80upx;
  color: #333;
  font-weight: bold;
  width: 200upx;
}
.t_input .inputs{
  color: #333;
  font-size: 32upx;
}
.inputs input{
  height: 80upx;
  line-height: 80upx;
  width: 100%;
  display: block;
}
.all_btn{
  font-size:24upx;
  font-family:Source Han Sans CN;
  font-weight:400;
  width: 150upx;
}
.jilu{
  height: 60upx;
  line-height: 60upx;
  padding: 0 4%;
  font-size:24upx;
  color: #999;
  font-family:Source Han Sans CN;
  font-weight:400;
  margin-top: 10upx;
}
.addclerk {
  width: 80%;
  height: 82upx;
  line-height: 82upx;
  border-radius: 15upx;
  font-size: 32upx;
  font-family: Source Han Sans CN;
  font-weight: 500;
  color: rgba(255, 255, 255, 1);
  text-align: center;
  position: fixed;
  bottom: 30upx;
  left: 50%;
  transform: translateX(-50%);
  background-color: pink;
}
.addclerk:active{
  opacity: 0.8;
}
</style>
<style>
page {
  background-color: #F5F5FA;
}
</style>