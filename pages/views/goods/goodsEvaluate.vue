<template>
	<!-- 商品评价 -->
	<view class="evaluate">
		<!-- 顶部tab -->
		<view class="tabs">
			<view v-for="(item, index) in tabList" :key="index" class="tabs_list"  @tap="setTabs(item, index)"
			 :style="'color:' + (active == index ?colors :'') + ';font-weight:' + (active == index ?'bold' : '500')">
				{{item.name}}({{item.number}})
				<view class="active" :style="'background:' + colors" v-if="active == index"></view>
			</view>
		</view>
		<!-- 评价列表 -->
		<view class="my_evaluate" >
			<view class="evaluate_box">
					<view class="pingjia">
						<view v-if="goodsEva.length !== 0">
							<view class="pingjia_box" v-for="(row, index) in goodsEva" :key="index">
								<view class="box_top">
									<image :src="row.headimg" mode="" class="head"></image>
									<view class="right">
										<p class="name">{{ row.nickname }}</p>
										<p class="p2">
											<text class="text1">{{ row.create_time }}</text>
											<text class="text2">{{ row.goods_name }}</text>
										</p>
										<p class="p3">
											<image src="/static/images/home/stars.png" v-for="s in row.score" :key="s" mode="" ></image>
											<block v-if="row.score !== 5">
												<image src="/static/images/home/star-no.png" v-for="(s,h) in (5-row.score)" :key="h" mode="" ></image>
											</block>
											</p>
									</view>
								</view>
									<view class="tag_box" v-if="row.tags.length !== 0">
										<view class="tags" v-for="(s,x) in row.tags" :key="x">{{s}}</view>
									</view>
								<view class="ping_neirong">{{ row.comment }}</view>
								<view class="ping_img" v-if="row.images.length !== 0 || row.video.length !== 0">
									<image :src="s" mode="" v-for="(s,x) in row.images" :key="x" @click="preview(row.images, x)"></image>
									<view class="video" v-for="(v, y) in row.video" :key="y">
										<video :src="v" :controls="false" :show-center-play-btn="false"></video>
										<view class="cover">
											<image src="../../../static/images/goods/bofang.png" mode="" @click="playVideo(v)"></image>
										</view>
									</view>
								</view>
								<!-- 回复 -->
								<view class="huifu" v-if="row.reply && row.reply !== ''">商家回复：{{ row.reply }}</view>
							</view>
							<p class="onbottom">—— 到底了 ——</p>
						</view>
					</view>
			</view>
		</view>
		<!-- 小程序单独的弹窗 -->
		<!-- #ifdef MP -->
		<view :class="'previewvideo ' + (showVideo == true ? 'onshowvideo':'')">
			<view class="close">
				<image src="/static/images/goods/close.png" @tap="closeVideo"></image>
			</view>
			<view class="videos">
				<video class="nowvideos" id="nowVideo" :src="videos" show-center-play-btn="false"
				 show-mute-btn="true" :show-fullscreen-btn="true">
				 </video>
			</view>
		</view>
		<!-- #endif -->
		<!-- H5小程序弹窗 -->
		<!-- #ifdef H5 -->
		<view :class="'previewvideo ' + (showVideo == true ? 'onshowvideo':'')">
			<view class="close">
				<image src="/static/images/goods/close.png" @tap="closeVideo"></image>
			</view>
			<view class="h5_videos" v-if="showVideo == true">
				<video class="nowvideos" id="nowVideo" :src="videos" show-center-play-btn="false"
				 show-mute-btn="true" :show-fullscreen-btn="true">
				 </video>
			</view>
		</view>
		<!-- #endif -->
		<nodata v-if="goodsEva.length == 0" :colors="colors"></nodata>
	</view>
</template>

<script>
	let app = getApp()
	import tabs from '../../commponent/public/tabs.vue'
	export default {
		components: {
			tabs
		},
		data() {
			return {
				colors: '',
				tabList: [{
					name: '最新',
					number: 10,
					id: 0
				}, {
					name: '好评',
					number: 8,
					id: 1
				}, {
					name: '中评',
					number: 8,
					id: 2
				}, {
					name: '差评',
					number: 6,
					id: 3
				}, {
					name: '有图',
					number: 5,
					id: 4
				}],
				active: 0,
				goodsEva:[ //评价列表
					{headimg:'/static/images/face.jpg',nickname:'反转',create_time:'2020-09-10 15:36',goods_name:'醇黑巧克力【20枚】', score:5,comment:'产品很不错,赞',images:['/static/images/goods/two.jpg','/static/images/goods/one.jpg'],reply:'感谢您的支持',tags:['价格合理','味道好','价格优惠','态度好'],video:[]},
					{headimg:'/static/images/face.jpg',nickname:'清风',create_time:'2020-09-10 13:36',goods_name:'醇黑巧克力【20枚】', score:4,comment:'针不错~',images:[],reply:'',tags:[],video:['https://fzdz.soft.haoyangsoft.com/uploads/system/videos/20200813/6c819d24ee6868aee33e150c4333329b.mp4']},
					{headimg:'/static/images/face.jpg',nickname:'明月',create_time:'2020-09-10 15:36',goods_name:'草莓味【8枚】', score:5,comment:'产品很不错,赞',images:['/static/images/goods/two.jpg','/static/images/goods/one.jpg'],reply:'感谢您的支持',tags:['价格合理','态度好'],video:[]},
				],
				temporary:[],
				videos:'',
				showVideo:false
			}
		},
		onReady() {
			this.nowVideo = uni.createVideoContext('nowVideo');
		},
		onLoad() {
			let colors = app.globalData.newColor; //设置主题颜色
			this.setData({
				colors: colors
			});
			this.temporary = this.goodsEva
		},
		methods:{
			setTabs(item,index){ //切换状态栏 模拟数据
				this.active = index
				this.goodsEva = index == 0 ? this.temporary : []
			},
			preview(imgs, index){ //预览图片
				uni.previewImage({
					current:index,
					urls:imgs
				})
			},
			playVideo(video){
				// #ifdef MP
				// 小程序下可以直接打开,但是在H5下部分浏览器会强制调用原生播放器播放
				this.videos = video
				this.showVideo = true
				setTimeout(()=>{
					this.nowVideo.play()
				},300)
				// #endif
				// #ifdef H5
				this.videos = video
				this.showVideo = true
				// #endif
			},
			closeVideo(){
				this.showVideo = false
				setTimeout(()=>{
					this.nowVideo.stop()
				},300)
			}
		}
	}
</script>

<style lang="scss" scoped>
	.tabs {
		height: 80upx;
		line-height: 80upx;
		display: flex;
		justify-content: flex-start;
		align-items: center;
		background-color: #fff;
		border-bottom: 1upx solid rgba($color: #eee, $alpha: 0.5);
		.tabs_list {
			flex: 1;
			text-align: center;
			font-size: 28upx;
			position: relative;
			color: #333;
		}
		.active {
			font-weight: bold;
			color: #DD4F42;
			width: 50upx;
			height: 4upx;
			border-radius: 20upx;
			background-color: #DD4F42;
			position: absolute;
			bottom: 0upx;
			left: 50%;
			transform: translateX(-50%);
			transition: all 0.3s;
		}
	}
	.my_evaluate{
		padding: 20upx 4%;
		.eva_title{
			height: 70upx;
			margin-top: 10upx;
			line-height: 70upx;
			font-size: 30upx;
			font-weight: bold;
			color: #202020;
			text{
				font-weight: normal;
			}
			.seeAll{
				float: right;
				font-size: 24upx;
				color: #999999;
				transform: translateY(6upx);
				&:active{
					opacity: 0.8;
				}
			}
		}
		.evaluate_box{
			.pingjia {
				background-color: #ffffff;
				overflow: hidden;
				.pingjia_box {
					overflow: hidden;
					margin-top: 20upx;
					padding-bottom: 20upx;
					overflow: hidden;
					border-bottom: 1upx solid #f2f2f2;
					&:last-child {
						border-bottom: none;
						padding-bottom: 10upx;
					}
					.box_top {
						display: flex;
						.head {
							height: 80upx;
							max-width: 80upx;
							min-width: 80upx;
							flex: 1;
							border-radius: 50%;
							float: left;
						}
						.right {
							flex: 1;
							margin-left: 20upx;
							position: relative;
							.name {
								font-size: 26upx;
								font-family: Source Han Sans CN;
								font-weight: 400;
								color: rgba(0, 0, 0, 1);
							}
							.p2 {
								margin-top: 20upx;
								font-size: 24upx;
								font-family: Source Han Sans CN;
								font-weight: 400;
								color: rgba(156, 156, 156, 1);
								.text2 {
									margin-left: 50upx;
								}
							}
							.p3 {
								height: 35upx;
								position: absolute;
								right: 0;
								width: 200upx;
								top: 8upx;
								image {
									width: 35upx;
									height: 35upx;
									float: left;
									margin-left: 5upx;
								}
							}
						}
					}
					.tag_box {
						margin-top: 20upx;
						overflow: hidden;
						.tags {
							font-size: 22upx;
							font-family: Source Han Sans CN;
							font-weight: 400;
							background-color: #FAEFF7;
							color: #FF546E;
							float: left;
							height: 50upx;
							line-height: 50upx;
							padding: 0 15upx;
							border-radius: 25upx;
							text-align: center;
							margin-right: 20upx;
						}
					}
					.ping_neirong {
						font-size: 28upx;
						margin-top: 20upx;
						font-family: Source Han Sans CN;
						font-weight: 500;
						color: rgba(0, 0, 0, 1);
						line-height: 50upx;
					}
					.ping_img {
						display: flex;
						flex-wrap: wrap;
						align-items: flex-start;
						align-content: flex-start;
						justify-content: space-between;
						margin-top: 20upx;
						&::after {
							content: '';
							max-width: 29vw;
							min-width: 29vw;
						}
						image,video {
							max-width: 29vw;
							min-width: 29vw;
							height: 29vw;
							border-radius: 10upx;
							margin-bottom: 20upx;
						}
						.video{
							position: relative;
							.cover{
								position: absolute;
								width: 100%;
								height: 100%;
								top: 0;
								left: 0;
								image{
									max-width: 60upx!important;
									min-width: 60upx!important;
									height: 60upx;
									position: absolute;
									top: 48%;
									left: 50%;
									z-index: 100;
									transform: translate(-50% ,-50%);
								}
							}
						}
					}
					.huifu {
						padding: 20upx;
						background-color: #f3f3f3;
						border-radius: 10upx;
						font-size: 24upx;
						font-family: Source Han Sans CN;
						font-weight: 400;
						color: rgba(77, 77, 77, 1);
					}
				}
			}
		}
	}
	.onbottom{
		height: 80upx;
		line-height: 80upx;
		text-align: center;
		font-size: 24upx;
		color: #ccc;
	}
	/* 预览视频弹窗 */
	.previewvideo {
		width: 100%;
		height: 100%;
		position: fixed;
		top: 0;
		left: 0;
		background-color: rgba($color: #000000, $alpha: 0.8);
		opacity: 0;
		z-index: -10;
		.close {
			width: 100vw;
			height: 8vh;
			line-height: 8vh;
			display: flex;
			align-content: center;
			align-items: flex-end;
			position: absolute;
			/* #ifdef MP */
			top: 0;
			/* #endif */
			/* #ifdef H5 */
			top: 60upx;
			/* #endif */
			left: 0;
			z-index: 100;
			image {
				width: 50upx;
				height: 50upx;
				display: block;
				justify-content: center;
				margin-left: 30upx;
				margin-bottom: 20upx;
				border-radius: 50%;
				padding: 10upx;
				background-color: rgba(0, 0, 0, 0.2);
			}
		}
		.videos {
			height: 100vh;
			width: 100vw;
			z-index: 10;
			position: relative;
			.nowvideos {
				width: 100%;
				height: 100%;
				margin: 0 auto;
				position: relative;
			}
		}
		.h5_videos{
			height: 50vh;
			width: 100vw;
			z-index: 10;
			margin-top: 25vh;
			.nowvideos {
				width: 100%;
				height: 100%;
				margin: 0 auto;
				position: relative;
			}
		}
	}
	.onshowvideo {
		opacity: 1;
		left: 0;
		z-index: 900;
	}
</style>
