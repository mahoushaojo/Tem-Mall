<template>
 <view class="threestage">
    <scroll-view class="left" scroll-y scroll-with-animation :scroll-top="scrollTop" @scrolltoupper="ontop">
      <view v-for="(item, index) in classList" :key="index" :class="'classTitle ' + (current == index ? 'actives':'')" @tap="setCurrent(item,index)" :style="{color:current == index ?colors:''}">
        <text>{{item.name}}</text>
        <view class="classactive" v-if="current == index" :style="'background-color:' + colors">
        </view>
      </view>
    </scroll-view>
    <scroll-view class="right" scroll-y scroll-with-animation>
      <block v-if="classData.length !== 0">
        <view class="top_img">
        <image src="https://img.zcool.cn/community/01e47655efeee832f875a132e43a9c.png@1280w_1l_2o_100sh.png" lazy-load="true" mode="widthFix"></image>
      </view>
      <view class="class_box">
        <view v-for="(item, index) in classData" :key="index" class="box_list" @tap="jumpClassList(item)">
          <image :src="item.img" mode="widthFix"></image>
          <view class="list_name">{{item.name}}</view>
        </view>
      </view>
      <view class="nodata">—— 到底啦 ——</view>
      </block>
      <view class="zanwu" v-if="classData.length == 0">
        <text class="iconfont icon-none" style="font-size: 32upx"></text>
        <text style="font-size: 24upx">暂无数据</text>
      </view>
    </scroll-view>
  </view>
</template>

<script>

export default {
  data() {
    return {
      scrollTop: 0
    };
  },

  components: {},
  props: {
    colors: {
      type: String
    },
	classList:{
		type:Array
	},
	classData:{
		type:Array
	},
	current: {
		type:Number,
		default: 0
	}
  },
  methods: {
    setCurrent(item,index) {
	 let scrollTop = index * 20;
	 if (index <= 2) {
		  scrollTop = 0;
	  }
      this.setData({
        current: index,
		scrollTop: scrollTop
      });
	  this.$emit('setThere',item)
    },
	jumpClassList(item){
		uni.navigateTo({
			url:'/pages/views/home/classList'
		})
	}
  }
};
</script>
<style scoped lang="scss">
@import "./threestage.scss";
</style>