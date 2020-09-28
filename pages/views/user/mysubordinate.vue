<template>
<view>
<!-- 我的下级 -->
<view class="mysubordinate">
  <view class="mysubordinate_list" v-for="(item, index) in subordinateList" :key="index">
    <image :src="item.headimg"></image>
    <view class="center">
      <view class="name">{{item.name}}</view>
      <view class="times">{{item.date}}</view>
    </view>
  </view>
  <view class="nodata">—— 到底啦 ——</view>
</view>
<nodata v-if="subordinateList.length == 0" :colors="colors" title="暂无下级"></nodata>
<loading v-if="isShow == true"></loading>
</view>
</template>

<script>
var app = getApp();
import loading from "../../commponent/public/loading";

export default {
  data() {
    return {
      isShow: true,
      colors: '',
	  subordinateList:[
		  {name:'清风', date:'2019-01-19 18：29',headimg:'https://sf3-ttcdn-tos.pstatp.com/img/user-avatar/18dd30f2613eca73ec247619c0da776c~120x256.image'},
		  {name:'良辰美景奈何天', date:'2019-02-19 18：29',headimg:'https://sf3-ttcdn-tos.pstatp.com/img/mosaic-legacy/3796/2975850990~120x256.image'},
		  {name:'流光忆暖', date:'2019-03-19 18：29',headimg:'https://sf1-ttcdn-tos.pstatp.com/img/user-avatar/413f21c22ea3b361678a6c6a734b2853~120x256.image'},
	  ]
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
  methods: {}
};
</script>
<style scoped lang="scss">
page{
  background-color: #F5F5FA;
}
.mysubordinate{
  margin-top: 10upx;
}
.mysubordinate_list{
  padding: 30upx 4%;
  background-color: #fff;
  display: flex;
  align-items: center;
  border-bottom: 1upx solid #eee;
}
.mysubordinate_list image{
  width: 100upx;
  height: 100upx;
  border-radius: 50%;
  margin-right: 20upx;
}
.mysubordinate_list .name{
  height: 60upx;
  line-height: 60upx;
  font-size: 28upx;
  font-weight: bold;
  color: #333;
}
.mysubordinate_list .times{
  color: #999;
  font-size: 24upx;
}
.nodata{
  color: #ccc;
  text-align: center;
  font-size: 24upx;
  margin-top: 20upx;
  margin-bottom: 20upx;
}
</style>