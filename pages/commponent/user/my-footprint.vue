<template>
	<view class="footprint">
		<list-cell icons="icon-zuji" linkUrl="/pages/views/user/allFootprint" :rightshow="true" :colors="colors">我的足迹</list-cell>
		<view class="footprint_box">
			<view class="nodata" v-if="logList.length == 0">
				<view>
					<text class="iconfont icon-zanwu1" :style="'color:' + colors"></text>
				</view>
				<view class="tips">先去浏览一些吧</view>
			</view>
			<scroll-view scroll-x class="footprint_scroll">
				<view v-for="(item, index) in logList" :key="index" @click="jumpNext(item)" class="scroll_box">
					<image :src="item.img" mode="aspectFill"></image>
					<view>{{item.name}}</view>
				</view>
			</scroll-view>
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
			logList: {
				type: Array
			},
			colors: {
				type: String
			}
		},
		methods: {
			jumpNext(item){
				uni.navigateTo({
					url:'/pages/views/goods/goodsDetails'
				})
			}
		}
	};
</script>
<style lang="scss" scoped>
	/*我的足迹*/
	.footprint {
		background-color: #fff;
		margin-bottom: 20upx;
	}

	.footprint .nodata {
		padding: 48upx 0;
		text-align: center;
	}

	.footprint .nodata text {
		font-size: 40upx;
	}

	.footprint .nodata .tips {
		font-size: 24upx;
		color: #999;
		padding-top: 20upx;
	}

	.footprint_scroll {
		background-color: #F8F8F8;
		width: auto;
		overflow-x: scroll;
		white-space: nowrap;
	}

	.scroll_box {
		width: 152upx;
		background-color: #fff;
		margin-top: 10upx;
		margin-right: 10upx;
		border-radius: 8upx;
		overflow: hidden;
		display: inline-block;
	}

	.scroll_box:active {
		opacity: 0.8;
	}

	.scroll_box image {
		width: 152upx;
		height: 152upx;
		display: block;
	}

	.scroll_box view {
		font-size: 24upx;
		overflow: hidden;
		white-space: nowrap;
		text-overflow: ellipsis;
		line-height: 40upx;
		padding: 10upx 5upx;
	}
</style>
