<template>
<view class="cencal_order">
  <view class="remark">
    <textarea maxlength="-1" placeholder="请在此处输入您的退单理由" placeholder-class="textarea_p"></textarea>
  </view>
  <view class="tag_box">
    <view v-for="(item, index) in remarkList" :key="index" class="tag_list" @tap="setCurrent(index)">
      <view :style="'color:' + (item.current == true ?'#fff':'') + ';background:' + (item.current == true ? colors:'') + ';border:' + (item.current == true ? 'none':'')">
        {{item.name}}
      </view>
    </view>
  </view>
  <view class="btns" :style="{background: colors}">
  	确认提交
  </view>
</view>
</template>

<script>
var app = getApp();
export default {
  data() {
    return {
      colors: '',
      remarkList: [{
        name: '图案不好看'
      }, {
        name: '性价比太低'
      }, {
        name: '态度不好'
      }, {
        name: '价格不合理'
      }, {
        name: '做工不行'
      }, {
        name: '物流时间长'
      }, {
        name: '价格优惠低'
      }, {
        name: '其他原因'
      }],
      data: ""
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
    setCurrent(index) {
      let remark = this.remarkList[index];
	  remark.current = !remark.current
      let data = 'remarkList[' + index + ']';
      this.setData({
        [data]: remark
      });
      let arr = [];
      this.remarkList.forEach(e => {
        if (e.current == true) {
          arr.push(e);
        }
      });
      console.log(arr);
    }

  }
};
</script>
<style lang="scss" scoped>
page{
  background-color: #F5F5FA;
}
.cencal_order{
  padding: 20upx 4%;
  background-color: #fff;
}
.remark{
  background-color: #F5F5F5;
  border-radius: 10upx;
  height: 60vw;
  padding: 20upx;
  margin-top: 20upx;
  textarea{
    font-size: 26upx;
    color: #797979;
  }
}
.textarea_p{
  font-size: 24upx;
  color: #797979;
}
.tag_box{
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  margin-top: 40upx;
}
.tag_box::after{
  content: '';
  width: 30%;
}
.tag_list{
  max-width: 24%;
  min-width: 24%;
  text-align: center;
  margin-bottom: 30upx;
}
.tag_list view{
  height: 60upx;
  line-height: 60upx;
  border-radius: 30upx;
  border: 1upx solid #ddd;
  font-size: 22upx;
  color: #303030;
}
.btns{
	width: 100%;
	height: 80upx;
	line-height: 80upx;
	font-size: 30upx;
	color: #FFFFFF;
	text-align: center;
	margin-top: 100upx;
	border-radius: 8upx;
}
</style>