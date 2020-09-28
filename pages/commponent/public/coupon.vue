<template>
<view>
 <view v-for="(item, index) in couponList" :key="index" class="coupon_box" @tap="onreceive(item, index)" >
      <view class="left">
        <view class="left_top">
        <text class="hui">券</text>
        <text class="hui_name">满{{item.money}}减{{item.reduce}}</text>
        </view>
        <view class="left_bottom">
        <text>有效日期：{{item.date}}</text>
        </view>
        	<image src="/static/images/goods/ylq.png" v-if="item.status == 1" class="ylq"></image>
      </view>
      <view class="right">
        <view class="money">￥{{item.reduce}}</view>
        <text>满{{item.money}}可用</text>
      </view>
      <view class="bottom">
        <view v-for="(row, indexs) in item.condition" :key="indexs">
        {{indexs+1}}.{{row}}
        </view>
      </view>
</view>
</view>
</template>

<script>

export default {
  data() {
    return {
      
    };
  },

  components: {},
  props: {
    colors: {
      type: String
    },
    couponList: {
      type: Array
    }
  },
  methods: {
    onreceive(item, index) { //领取优惠券
	  let that = this
      if (item.status == 1) {
        return;
      } else {
		// 向上层传递领取优惠券
		this.$emit('onReceive', item, index)
      }
    }

  }
};
</script>
<style lang="scss" scoped>
.coupon_box{
  margin: 20upx;
  padding: 20upx;
  box-shadow: 0upx 0upx 10upx #ddd;
  position: relative;
  border-radius: 10upx;
  padding-bottom: 10upx;
  overflow: hidden;
  
}
.coupon_box .left{
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  border-bottom: 1upx solid #eee;
  padding-bottom: 20upx;
  position: relative;
}
.coupon_box .left .ylq{
  width: 60upx;
  height: 45upx;
  position: absolute;
  top: 0;
  right: 140upx;
}
.coupon_box .left .hui{
  width: 40upx;
  height: 40upx;
  font-size: 22upx;
  color: #fff;
  background-color: rgba(255, 84, 110,.8);
  border-radius: 8upx;
  line-height: 40upx;
  text-align: center;
  display: inline-block;
  transform: translateY(-5upx);
}
.coupon_box .left .left_top{
  width: 80vw;
  display: block;
  font-size: 26upx;
  font-weight: bold;
  
}
.left_top .hui_name{
  line-height: 60upx;
  height: 60upx;
  margin-left: 20upx;
  display: inline-block; 
  font-size: 28upx;
}
.left_bottom{
  font-size: 24upx;
  font-weight: 500;
  color: #333;
  height: 60upx;
  line-height: 60upx;
}
.coupon_box .right{
  position: absolute;
  right: 20upx;
  top: 25upx;
  text-align: center;
}
.coupon_box .right .money{
  font-size: 45upx;
  margin-bottom: 10upx;
}
.coupon_box .right text{
  font-size: 24upx;
  color: #999;
}
.coupon_box .bottom{
  height: 60upx;
  line-height: 60upx;
  display: flex;
  align-content: flex-start;
  font-size: 24upx;
  margin-top: 10upx;
}
.coupon_box .bottom view{
  margin-right: 20upx;
  color: #888;
}
</style>