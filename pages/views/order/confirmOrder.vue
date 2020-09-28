<template>
<view class="order">
  <!-- 选择配送方式 -->
  <view class="mode" @tap="selectMode">
    <text class="text1">配送方式</text>
    <text class="text2">{{modes || "请选择配送方式"}}</text>
    <image src="/static/images/home/right.png"></image>
  </view>
  <!-- 收货地址 -->
  <view class="order_address" v-if="tapIndex == 0">
    <image src="/static/images/home/bottom.png"></image>
    <view class="address_box" @tap="setAddress">
        <view class="weizhi_icon">
          <text class="iconfont icon-dizhi" :style="'color:' + colors"></text>
        </view>
      <block v-if="address.name && address.name !== ''">
        <view class="center">
          <view class="name">
            <text class="text1">{{address.name}}</text>
            <text class="phones">{{address.phone}}</text>
           </view>
            <view class="address_name">{{address.address_name}}</view>
        </view>
      </block>
      <view class="noaddress" v-else>
      请添加收货地址
      </view>
    </view>
  </view>
  <!-- 商品详情 -->
  <view v-for="(item, index) in goodsList" :key="index" class="goods">
    <view class="goods_data">
	  <image :src="item.selectSku.img" mode="widthFix" v-if="item.selectSku.img"></image>
      <image :src="item.img" mode="widthFix" v-else></image>
      <view class="goods_title">
        <view class="g_name">
          {{item.title}}
        </view>
        <view class="goods_sku">
        规格: <text style="margin-right: 10upx;">{{item.selectSku.goods_sku_text || '暂无规格'}}</text>
        </view>
        <view class="price">
            <view class="t1" :style="'color:' + colors">￥{{item.money}}</view>
            <view class="t2">
              <text>￥{{item.hmoney}}</text>
            </view>
            <view class="t3">
              x{{item.number}}
            </view>
          </view>
      </view>
    </view>
    <view class="morelist" style="border-bottom:none">
      <view class="title">
        <text class="quan" :style="'background:' + colors">券</text>
         <text>优惠券</text>
      </view>
      <view class="right_title" :style="'color:' + colors + ';font-size:24upx'" @tap="openCoupon(index)">
        {{item.couponName || '请选择优惠券'}}
      </view>
    </view>
  </view>
  <!-- 订单详情 -->
  <view class="order_more">
    <view class="morelist">
      <text class="title">商品总价</text>
      <view class="right_title">
        ￥{{sumprice}}
      </view>
    </view>
    <view class="morelist">
      <view class="title">
        <text class="quan" :style="'background:' + colors">运</text>
         <text>运费</text>
      </view>
      <view class="right_title">
        ￥0.00
      </view>
    </view>
    <view class="morelist">
      <text class="title">实付款</text>
      <view class="right_title" :style="'color:' + colors + ';'">
        ￥{{sumprice}}
      </view>
    </view>
    <view class="tips">
      <view class="tips_name">备注信息</view>
      <view class="textarea_box">
        <textarea placeholder="请输入备注信息" placeholder-class="font-size: 24upx" maxlength="-1" v-if="couponshow == false"></textarea>
      </view>
    </view>
  </view>
  <view class="bottom_btn">
    <view class="moneys">
    合计: <text :style="'color:' + colors + ';'">￥{{sumprice}}</text>
    </view>
    <view class="btns" :style="'background:' + colors + ';'" @tap="submit">
    提交订单
    </view>
  </view>
  <!-- 优惠券弹出层 -->
  <view class="mask" catchtouchmove="preventTouchMove" v-if="couponshow == true" @tap="hidecoupon"></view>
  <view class="coupon" :style="'bottom:' + (couponshow == true ? '0upx':'')">
    <view class="buyong" @click="notUsed()">不使用优惠券</view>
    <scroll-view class="scrolls" scroll-y>
     <coupon :couponList="couponList" @onReceive="onReceive"></coupon>
    </scroll-view>
  </view>
</view>
</template>

<script>
var app = getApp();
import coupon from "../../commponent/public/coupon";
import {getGoodsData,getAddress,removeAddress} from '@/utils/auth.js'
export default {
  data() {
    return {
      colors: '',
      couponshow: false,
      modes: '',
	  tapIndex: 99,
	  goodsList:getGoodsData(),
	  couponIndex: 0,
	  nowprice: 0, //临时存储总金额的变量 用于计算优惠券
	  sumprice: 0,
	  address:{
		  name:'反转',
		  phone: 12345678915,
		  address_name:'北京市海淀区苏家坨乡前沙涧村15号'
	  },
	  couponList: [ //优惠券列表
	  	{
	  		money: 30,
	  		reduce: 5,
	  		date: '2020-02-09 2020-10-02',
	  		id: 1,
	  		status: 0,
	  		condition: ['新人专享', '仅在支付时使用', '可与其他产品共享']
	  	}, {
	  		money: 30,
	  		reduce: 5,
	  		date: '2020-02-09 2020-10-02',
	  		id: 2,
	  		status: 0,
	  		condition: ['新人专享']
	  	}
	  ]
    };
  },

  components: {
    coupon
  },
  props: {},

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
	removeAddress() //清空存储的地址
	// 计算所有的商品总价
	this.getSumPrice()
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
  onShow: function () {
	  let address = getAddress() //判断是否存在重新选择的地址
	  if(address && address.name){
		  this.address = address
	  }
  },

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () {
	  
  },

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
	getSumPrice(){
		let sumprice = 0
		this.goodsList.forEach(e=>{
			sumprice = (sumprice+Number(e.money)).toFixed(2)
		})
		this.sumprice = sumprice
		this.nowprice = sumprice 
	},
    openCoupon(index) {
      this.setData({
        couponshow: true,
		couponIndex: index
      });
    },

    hidecoupon() {
      this.setData({
        couponshow: false
      });
    },

    submit() {
      //提交订单
      uni.navigateTo({
        url: '/pages/views/order/success'
      });
    },

    selectMode() {
      let that = this;
	  let list = ['物流寄送', '到店自提']
      uni.showActionSheet({
        itemList: list,
        success: function (res) {
			that.setData({
			  modes: list[res.tapIndex],
			  tapIndex: res.tapIndex
			});
        }
      });
    },
	
    setAddress() {
      uni.navigateTo({
        url: '/pages/views/user/myaddress'
      });
    },
	onReceive(item, index){ //选择优惠券
		this.couponshow = false
		/**
		 * 自定义变量 到goodsList中 用户计算合计金额与优惠券
		 */
		this.goodsList[this.couponIndex].couponName = '满'+item.money+'减'+item.reduce
		this.goodsList[this.couponIndex].couponReduce = item.reduce //优惠券金额
		this.sumprice = this.sumprice - item.reduce
	},
	notUsed(){ //不使用优惠券 重置金额
		this.couponshow = false
		this.goodsList[this.couponIndex].couponName = ''
		this.sumprice = this.sumprice + Number(this.goodsList[this.couponIndex].couponReduce)
	}
  }
};
</script>
<style lang="scss" scoped>
page {
  background-color: #FFFFFF;
}

.order {
  padding: 20upx 4%;
}
.mode{
  height: 80upx;
  line-height: 80upx;
  display: flex;
  justify-content: space-between;
  background-color: #fff;
  padding: 0 20upx;
  border-radius: 10upx;
  align-items: center;
  margin-bottom: 20upx;
  box-shadow: 0upx 0upx 10upx #ddd;
}
.mode:active{
  background-color: #f5f5f5;
}
.mode .text1{
  color: #999;
  font-size: 24upx;
}
.mode .text2{
  font-size: 24upx;
  color: #333;
  display: block;
  width: 50%;
  font-weight: bold;
}
.mode image{
  width: 40upx;
  height: 40upx;
  display: block;
}
.order_address {
  height: 150upx;
  width: 100%;
  background-color: #fff;
  border-radius: 10upx;
  overflow: hidden;
  position: relative;
  box-shadow: 0upx 0upx 10upx #ddd;
}

.order_address image {
  width: 100%;
  height: 100%;
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 10;
}

.address_box {
  width: 100%;
  height: 100%;
  display: block;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 10;
  box-sizing: border-box;
  padding: 20upx;
  display: flex;
  align-items: center;
}

.weizhi_icon text {
  font-size: 40upx;
  margin-left: 10upx;
}

.address_box .center {
  margin-left: 20upx;
}

.address_box .center .name {
  height: 60upx;
  line-height: 60upx;
}

.address_box .center .name .text1 {
  font-size: 26upx;
  font-weight: bold;
  color: #333;
  display: inline-block;
  margin-right: 20upx;
}

.phones {
  font-size: 24upx;
  color: #999;
  z-index: 0;
}

.address_box .address_name {
  font-size: 26upx;
  font-weight: bold;
  color: #333;
}

.noaddress {
  margin-left: 40upx;
  font-weight: bold;
  color: #333;
  font-size: 26upx;
}

.goods {
  background-color: #fff;
  margin-top: 20upx;
  box-shadow: 0upx 0upx 10upx #ddd;
  border-radius: 10upx;
  padding: 20upx;
  padding-bottom: 10upx;
}

.goods_data {
  width: 100%;
  display: flex;
  margin-bottom: 15upx;
}

.goods_data image {
  min-width: 150upx;
  max-width: 150upx;
  height: 150upx;
  display: block;
  border-radius: 10upx;
}

.goods_title {
  margin-left: 20upx;
  line-height: 40upx;
  width: 100%;
}
.goods_title .price{
  width: 100%;
  display: flex;
  height: 40upx;
  line-height: 40upx;
  margin-top: 20upx;
  position: relative;
}
.goods_title .price .t1{
  font-size: 30upx;
  font-weight: bold;
  display: block;
}
.goods_title .price .t2{
  font-size: 24upx;
  margin-left: 15upx;
  display: block;
  color: #999;
  text-decoration: line-through;
}
.goods_title .price .t3{
  float: right;
  font-weight: bold;
  font-size: 28upx;
  color: #999;
  position: absolute;
  right: 0upx;
  top: 0;
}
.goods_title .g_name {
  font-size: 26upx;
  font-weight: bold;
  max-height: 80upx;
  overflow: hidden;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}
.goods_title .goods_sku{
  color: #999;
  /* margin-top: 20upx; */
}
.numbers{
  /* text-align: right; */
}
.shop{
  margin-top: 20upx;
  font-size: 24upx;
  color: #333;
  display: flex;
  align-content: center;
}
.shop .iconfont{
  margin-right: 20upx;
}
.order_more{
  padding: 0 2%;
  margin-top: 20upx;
}
.morelist{
  height: 80upx;
  line-height: 80upx;
  display: flex;
  justify-content: space-between;
  border-bottom: 1upx dashed #eee;
}
.morelist .title{
  color: #333;
  font-size: 26upx;
  font-weight: bold;
  display: flex;
  align-items: center;
}
.morelist .title .quan{
  font-size: 20upx;
  width: 35upx;
  height: 35upx;
  line-height: 36upx;
  text-align: center;
  border-radius: 8upx;
  margin-right: 10upx;
  align-items: center;
  color: #fff;
}
.morelist .right_title{
  color: #999;
}
.tips{
  padding: 10upx 0;
  margin-bottom: 120upx;
}
.tips .tips_name{
  font-size: 26upx;
  font-weight: bold;
  line-height: 60upx;
}
.textarea_box{
  min-height: 100upx;
  width: 100%;
  border: 1upx solid #eee;
  border-radius: 10upx;
  padding: 20upx;
}
.textarea_box textarea{
  font-size: 24upx;
  height: 150upx;
}
.bottom_btn{
  height: 100upx;
  width: 100vw;
  background-color: #fff;
  position: fixed;
  left: 0;
  bottom: 0;
  line-height: 100upx;
  display: flex;
  justify-content: flex-end;
  padding: 0 30upx;
  z-index: 100;
  font-weight: bold;
}
.bottom_btn .moneys{
  font-size: 28upx;
  font-weight: bold;
  margin-right: 100upx;
}
.bottom_btn .btns{
  font-size: 28upx;
  color: #fff;
  height: 60upx;
  line-height: 60upx;
  padding: 0 25upx;
  text-align: center;
  border-radius: 40upx;
  margin-top: 20upx;
  font-weight: bold;
}
/* 优惠券 */
.coupon{
  background-color: #fff;
  border-radius: 10upx 10upx 0 0;
  position: fixed;
  left: 0;
  bottom: -1000upx;
  z-index: 150;
  transition: all 0.3s;
}
.coupon .buyong{
  line-height: 80upx;
  padding: 0 4%;
  text-align: right;
  color: #999;
}
.mask{
  width: 100%;
  height: 100%;
  position: fixed;
  top: 0;
  left: 0;
  background: #000;
  z-index: 10;
  opacity: 0.7;
}
.scrolls{
  width: 100vw;
  height: 55vh;
  z-index: 500;
}
</style>