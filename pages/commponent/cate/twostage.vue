<template>
 <view class="towstage">
    <scroll-view class="left" :scroll-y="true" scroll-with-animation :scroll-top="scrollTop">
      <view v-for="(item, index) in classList" :key="index" :class="'classTitle ' + (current == index ? 'actives':'')" @tap="setCurrent(item, index)" :style="{color:current == index ?colors:''}">
        <text>{{item.name}}</text>
        <view class="classactive" v-if="current == index" :style="'background-color:' + colors">
        </view>
      </view>
    </scroll-view>
    <scroll-view class="right" :scroll-y="true" :scroll-with-animation="true" >
      <view class="screen" v-if="hideShow == true">
        <view class="xiaoliang" @tap="setPrice">
          <text class="name">价格</text>
          <view class="icon">
            <text class="iconfont icon-shangsanjiao" :style="'color:' + (Price == '0' ? colors :'')"></text>
            <text class="iconfont icon-xiasanjiao" :style="'color:' + (Price == '1' ? colors :'')"></text>
          </view>
        </view>
        <view class="xiaoliang" @tap="setSales">
          <text class="name">销量</text>
          <view class="icon">
            <text class="iconfont icon-shangsanjiao" :style="'color:' + (sales == '0' ? colors :'')"></text>
            <text class="iconfont icon-xiasanjiao" :style="'color:' + (sales == '1' ? colors :'')"></text>
          </view>
        </view>
      </view>
	  <view class="place" v-if="hideShow == true">
	  </view>
      <view class="goods_list" v-if="dataList.length !== 0">
        <view v-for="(item, index) in dataList" :key="index" class="goods_item">
          <image :src="item.img" @tap="jumpDetail" :data-item="item"></image>
          <view class="goods_right">
            <view class="goods_name" @tap="jumpDetail" :data-item="item">{{item.title}}</view>
            <view class="numbers">
              <text v-if="item.youhui == true">优惠券</text>
              <text v-if="item.baoyou == true">包邮</text>
            </view>
            <view class="price">
              <text class="money">￥{{item.money}}</text>
              <text class="hx_money">￥{{item.hmoney}}</text>
              <text class="iconfont icon-gouwuche gouwuche" @tap="addCart(item)"></text>
            </view>
          </view>
        </view>
        <view class="nodata">—— 到底啦 ——</view>
      </view>
      <view class="zanwu" v-if="dataList.length == 0">
        <text class="iconfont icon-none" style="font-size: 32upx"></text>
        <text style="font-size: 24upx">暂无商品</text>
      </view>
    </scroll-view>
  <!-- 弹出规格选项 -->
   <sku :skuList="nowList" :showModal="showModal" :colors="colors" @onhide="onhide" bottoms="100"></sku>
  </view>
</template>

<script>
import sku from '../public/sku.vue'
export default {
  data() {
    return {
      scrollTop: 0,
      Price: '',
      //价格排序
      sales: '',
      //销量排序
      showModal: false,
      nowList: {},
    };
  },

  components: {
	  sku
  },
  props: {
    colors: {
      type: String
    },
    hideShow: {
      //控制是否显示筛选
      type: Boolean,
      default: true
    },
	classList:{
		type:Array
	},
	dataList:{
		type:Array
	},
	current:{
		type: Number,
		default: 0
	}
  },
  methods: {
    setCurrent(item,index) {
      //设置tab
      let scrollTop = index * 20;
      if (index <= 2) {
        scrollTop = 0;
      }
      this.setData({
        current: index,
        scrollTop: scrollTop,
      });
	  this.$emit('setTwo', item)
    },

    setPrice() {
      //价格排序
      let price = this.Price;
	  /**
	   * 0 代表升序
	   * 1 代表降序
	   */
      if (price == '') {
        this.setData({
          Price: '0',
          sales: ''
        });
      } else if (price == '0') {
        this.setData({
          Price: '1'
        });
      } else if (price == '1') {
        this.setData({
          Price: '0'
        });
      }
    },

    setSales() {
      //销量排序
      let sales = this.sales;
	  /**
	   * 0 代表升序
	   * 1 代表降序
	   */
      if (sales == '') {
        this.setData({
          sales: '0',
          Price: ''
        });
      } else if (sales == '0') {
        this.setData({
          sales: '1'
        });
      } else if (sales == '1') {
        this.setData({
          sales: '0'
        });
      }
    },
	addCart(item){
		this.nowList = item
		this.showModal = true
	},
    onhide() {
      //隐藏规格对话框
      this.showModal = false
    },
    jumpDetail(e) {
      uni.navigateTo({
        url: '/pages/views/goods/goodsDetails'
      });
    }

  }
};
</script>
<style scoped lang="scss">
@import "./twostage.scss";
</style>