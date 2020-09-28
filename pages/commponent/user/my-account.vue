<template>
<view class="my_money">
    <list-cell icons="icon-qianbao" :colors="colors" :rightshow="false">我的账户</list-cell>
    <view class="menu_btnbox">
      <view class="menu_btns" @tap="withdrawa">
        <view class="p1">{{balance}}</view>
        <view class="p2">余额</view>
      </view>
       <view class="menu_btns" @tap="mycoupon">
        <view class="p1">{{coupon}}</view>
        <view class="p2">优惠券</view>
      </view>
       <view class="menu_btns" @tap="mypoints">
        <view class="p1">{{integral}}</view>
        <view class="p2">积分</view>
      </view>
    </view>
  </view>
</template>

<script>
import listCell from "./list-cell";

export default {
  data() {
    return {};
  },

  components: {
    listCell
  },
  props: {
    balance: {
      //余额
      type: Number,
      default: 0
    },
    coupon: {
      //优惠券
      type: Number,
      default: 0
    },
    integral: {
      //积分
      type: Number,
      default: 0
    },
    colors: {
      type: String
    }
  },
  methods: {
    mycoupon() {
      uni.navigateTo({
        url: '/pages/views/user/mycoupon'
      });
    },

    mypoints() {
      uni.navigateTo({
        url: '/pages/views/user/mypoints'
      });
    },
	withdrawa(){
	  uni.navigateTo({
		url: '/pages/views/withdrawal/withdrawal'
	  });
	}
  }
};
</script>
<style lang="scss" scoped>
.my_money {
  background-color: #fff;
  margin-bottom: 20upx;
}
.menu_btnbox {
  display: flex;
  align-items: center;
}
.menu_btns {
  text-align: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  flex: 1;
  margin: 30upx 0;
  font-size: 24upx;
  color: #75787d;
  line-height: 40upx;
}
.menu_btns .p1{
  font-size: 30upx;
  font-weight: bold;
}
</style>