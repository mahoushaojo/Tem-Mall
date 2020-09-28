<template>
 <view class="my_order">
    <list-cell icons="icon-order" linkUrl="/pages/views/order/orderlist" :colors="colors">全部订单</list-cell>
    <view class="order_btnbox">
      <view v-for="(item, index) in orderText" :key="index" class="order_btns" @tap="jumpOrder(item,index)">
        <view>
          <text :class="'iconfont ' + item.icon" :style="'color:' + colors"></text>
        </view>
        <view style="color:#666">{{item.name}}</view>
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
    orderText: {
      type: Array
    },
    colors: {
      type: String
    }
  },
  methods: {
    jumpOrder(item,index) {
	  if(item.url !== ''){
		  uni.navigateTo({
		    url: item.url
		  });
	  }else{
		  uni.navigateTo({
		    url: '/pages/views/order/orderlist?tabIndex=' + item.id
		  });
	  }
    }
  }
};
</script>
<style lang="scss" scoped>
.my_order{
  background-color: #fff;
  margin-bottom: 20upx;
}
.order_btnbox {
  display: flex;
  align-items: center;
}
.order_btns {
  text-align: center;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  flex: 1;
  margin: 10upx 0 20upx;
  padding: 20upx 0 10upx;
  font-size: 24upx;
  color: #75787d;
  line-height: 40upx;
}
.order_btns text{
  font-size: 36upx;
}
</style>