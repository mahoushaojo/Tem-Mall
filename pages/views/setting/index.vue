<template>
<view class="setting">
   <view class="cell_box">
    <item-cell cellname="关于商城" ></item-cell>
    <item-cell cellname="站点帮助" ></item-cell>
    <item-cell cellname="意见反馈" ></item-cell>
   </view>
   <!-- 选择主题 -->
   <view class="cu-list menu sm-border card-menu">
			<view class="cu-item">
				<view class="content flex align-center">
					<text :class="'cuIcon-colorlens text-' + themeColor.name"></text>
					<view :class="'padding solid radius shadow-blur bg-' + themeColor.name"></view>
					<view class="title">主题色：<text :class="'text-' + themeColor.name">{{themeColor.title}}</text></view>
				</view>
				<view class="action">
					<button @tap="showColorModal" :class="'cu-btn round shadow bg-' + themeColor.name">
						<text class="cuIcon-colorlens"></text> 选择主题
					</button>
				</view>
			</view>
		</view>
    <!-- 选择颜色模态框 -->
    <view :class="'cu-modal ' + (colorModal == true?'show':'')">
			<view class="cu-dialog">
				<view class="cu-bar justify-end solid-bottom">
					<view class="content">选择颜色</view>
					<view class="action" @tap="closeColor">
						<text class="cuIcon-close text-red"></text>
					</view>
				</view>
				<view class="grid col-5 padding">
					<view v-for="(item, index) in themeList" :key="index" class="padding-xs" @tap="SetColor(item)">
						<view :class="'padding-tb radius bg-' + item.name" style="color:#ffffff">
							{{ item.title }}
						</view>
					</view>
				</view>
			</view>
		</view>
    <!-- 退出登录 -->
    <view class="exit" :style="'color:' + themeColor.color" @tap="onexit">
      退出登录
    </view>
</view>
</template>

<script>
var app = getApp();
import { setConfig, getConfig, removeToken } from "../../../utils/auth";
import itemCell from "../../commponent/setting/item-cell";
export default {
  data() {
    return {
      themeColor: {},
      colorModal: false,
      themeList: app.globalData.config
    };
  },

  components: {
    itemCell
  },
  props: {},

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    let config = getConfig();
    this.setData({
      themeColor: config
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
    showColorModal() {
      this.setData({
        colorModal: true
      });
    },

    closeColor() {
      this.setData({
        colorModal: false
      });
    },

    SetColor(item) {
      setConfig(item);
      this.setData({
        themeColor: item,
        colorModal: false
      });
      app.globalData.newColor = item.color;
    },
    onexit() {
      //退出登录
      uni.showModal({
        cancelColor: '#999',
        content: '确认要退出登录吗?',
        confirmColor: this.themeColor.color,
        success: function (res) {
          if (res.confirm) {
            removeToken();
            uni.showToast({
              title: '退出成功',
              icon: 'none'
            });
            setTimeout(() => {
              uni.switchTab({
                url: '../tabBar/home'
              });
            }, 1500);
          }
        }
      });
    }
  }
};
</script>
<style scoped lang="scss">
.bg-default{
  background-color: #fa436a;
  color: #fff;
}
.text-default{
  color: #fa436a;
}
.cu-item .title{
  margin-left: 20upx;
  font-size: 28upx;
}
.action button{
  width: 200upx!important;
  font-size: 24upx;
  color: #fff;
}
.card-menu{
  margin: 0!important;
  padding-top: 20upx;
}
.cell_box{
  padding-top: 20upx;
}
.exit{
  width: 100%;
  height: 80upx;
  line-height: 80upx;
  text-align: center;
  background-color: #FFFFFF;
  font-size: 30upx;
  position: fixed;
  left: 0;
  bottom: 50upx;
}
</style>
<style>
page{
	background-color: #F8F8F8!important;
}
</style>