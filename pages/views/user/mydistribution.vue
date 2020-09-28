<template>
<view>
<view class="distribution">
  <view class="userinfo" :style="'background:' + colors">
    <image :src="userinfo.avatarUrl" v-if="userinfo.avatarUrl"></image>
	<image src="../../../static/images/face.jpg" v-else></image>
    <view class="user_name">
      <view class="p1">{{userinfo.nickName || '张三'}}</view>
      <view class="p2">推荐人：李四</view>
    </view>
  </view>
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
  <view class="myfunction">
    <my-server :serverList="serverList" :colors="colors" titles="常用功能" icons="icon-icon_type"></my-server>
  </view>
</view>
<loading v-if="isShow == true"></loading>
</view>
</template>

<script>
var app = getApp();
import myServer from "../../commponent/user/my-server";
import loading from "../../commponent/public/loading";
import {getUserInfo} from '../../../utils/auth.js'
export default {
  data() {
    return {
      colors: '',
      serverList: [{
        name: '我的下级',
        icon: 'icon-fenxiao',
        url: '/pages/views/user/mysubordinate'
      }, {
        name: '推广分享',
        icon: 'icon-fenxiang1',
        url: '/pages/views/user/extension'
      }],
      isShow: true,
	  userinfo: getUserInfo() || {}
    };
  },

  components: {
    myServer,
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
.userinfo{
  display: flex;
  justify-content: flex-start;
  padding: 20upx 4%;
  border-bottom: 1upx solid rgba(208, 205, 205, 0.5);
}
.userinfo image{
  width: 120upx;
  height: 120upx;
  display: block;
  border-radius: 50%;
  margin-right: 20upx;
}
.userinfo .user_name .p1{
  height: 60upx;
  line-height: 60upx;
  color: #fff;
  font-weight: bold;
  font-size: 28upx;
}
.userinfo .user_name .p2{
  font-weight: bold;
  color: #fff;
  font-size: 24upx;
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
  padding:8upx 20upx;
  font-size: 24upx;
  color: #fff;
  border: 1upx solid #fff;
  border-radius: 40upx;
  margin-top: 60upx;
}
.myfunction{
  margin: 0 4%;
  margin-top: 30upx;
  box-shadow: 0upx 0upx 10upx #ddd;
  border-radius: 10upx;
}
</style>