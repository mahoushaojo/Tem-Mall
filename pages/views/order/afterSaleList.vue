<template>
	<view>
		<view class="order_list">
			<view style="background-color: #FFFFFF;">
				<navBar :showLeft="true" navTitle="退换/售后" textcolor="#000" :leftBg="false"></navBar>
			</view>
			<!-- 顶部tab -->
			<view class="nav_top" style="border-bottom: 1upx solid #F8F8F8;">
				<tabs :colors="colors" :tabList="tabList" :active="active" @setTabs="setTabs"></tabs>
			</view>
			<scroll-view class="list_box" :scroll-y="true" @scrolltolower="ongetMoreList">
				<block v-if="active !== 2">
					<view v-for="(item, index) in orderList" :key="index" class="lists">
						<view v-for="(row, index2) in item.goods" :key="index2" class="top">
							<view class="top_box">
								<image class="cover" :src="row.img" mode="aspectFill"></image>
								<view class="top_right">
									<view class="order_name">{{row.title}}</view>
									<view class="sku">规格：{{row.goods_sku_text || '暂无规格'}}</view>
									<view class="price">
										<view class="t1">￥{{row.money}}</view>
										<view class="t3">
											x{{row.number}}
										</view>
									</view>
								</view>
							</view>
							<view class="bottom">
								<block v-if="active == 0">
									<view @click="onafterSale(row)" :style="'color:' + colors + ';border-color:' + colors">
										申请售后
									</view>
								</block>
								<block v-if="active == 1">
									<view @click="onwithdraw(row)" :style="'color:' + colors + ';border-color:' + colors">
										取消申请
									</view>
								</block>
							</view>
						</view>
					</view>
					<view class="nodata" v-if="orderList.length >= 3">—— 到底啦 ——</view>
					<nodata :colors="colors" title="暂无订单信息" v-if="orderList.length == 0"></nodata>
				</block>
				<block v-if="active == 2">
					<view v-for="(item, index) in afterList" :key="index" class="lists">
						<view class="top">
							<view class="top_box">
								<image class="cover" :src="item.img" mode="aspectFill"></image>
								<view class="top_right">
									<view class="order_name">{{item.title}}</view>
									<view class="sku">规格：{{item.goods_sku_text || '暂无规格'}}</view>
									<view class="price">
										<view class="t1">￥{{item.money}}</view>
										<view class="t3">
											x{{item.number}}
										</view>
									</view>
								</view>
							</view>
							<view class="bottom">
								<view class="success">
									<text class="success_type">完成</text>
									<text class="success_t2">服务已完成，感谢您的支持</text>
									<image class="right_img" src="../../../static/images/home/right.png" mode=""></image>
								</view>
							</view>
						</view>
					</view>
					<view class="nodata" v-if="afterList.length >= 3">—— 到底啦 ——</view>
					<nodata :colors="colors" title="暂无订单信息" v-if="afterList.length == 0"></nodata>
				</block>
			</scroll-view>
		</view>
		<loading v-if="isShow == true"></loading>
	</view>
</template>

<script>
	var app = getApp();
	import tabs from "../../commponent/public/tabs";
	import loading from "../../commponent/public/loading";
	import navBar from '../../commponent/public/navBar.vue'
	export default {
		data() {
			return {
				statusBarHeight: app.globalData.statusHeight + 'px',
				toBarHeight: app.globalData.toBar + 'px',
				tabList: [{
					name: '售后申请',
					id: 0
				}, {
					name: '处理中',
					id: 1
				}, {
					name: '申请记录',
					id: 2
				}],
				active: 0,
				orderList: [{
						goods: [{
								title: 'DUNKINDONUTS唐恩都乐美国甜甜圈6个礼盒装 随机搭配6款',
								type: 1,
								goods_id: 201,
								number: 1,
								goods_sku_text: '醇黑巧克力【20枚】',
								img: 'http://img10.360buyimg.com/n1/jfs/t1/86401/35/12206/357766/5e43b59cE5a7aa4dd/0753be765166c195.jpg',
								money: '175.78',
							},
							{
								title: '农谣人 原味火山石烤肠1000g/约16根台式原味肠地道肠纯肉肠热狗肠台湾烤肠香肠烧烤肠半熟食火腿肠 台式原味地道肠1kg',
								type: 1,
								goods_id: 204,
								number: 1,
								goods_sku_text: '台式原味地道肠1kg',
								img: 'http://img10.360buyimg.com/n1/jfs/t1/118993/11/329/175715/5e8ac0afE94234346/3ceb1344cf34d655.jpg',
								money: '52.00 '
							},
						],
						type: 1,
						status: 0,
						order_No: 'AQWEAD45648974974456',
						shopp_Address: '北京市海淀区苏家坨乡前沙涧村'
					},
					{
						goods: [{
							title: '钟薛高 钟意你系列 特牛乳*4片 丝绒可可*4片 半巧主义*2 冰淇淋生鲜雪糕 10片装',
							img: '/static/images/goods/there.jpg',
							goods_id: 203,
							money: '152.00 ',
							sku: '',
							number: 1,
						}, ],
						type: 1,
						status: 0,
						order_No: 'AQWEAD45648974974456',
						shopp_Address: '北京市海淀区苏家坨乡前沙涧村'
					},
				],
				afterList: [{
						title: 'DUNKINDONUTS唐恩都乐美国甜甜圈6个礼盒装 随机搭配6款',
						type: 1,
						goods_id: 201,
						number: 1,
						goods_sku_text: '醇黑巧克力【20枚】',
						img: 'http://img10.360buyimg.com/n1/jfs/t1/86401/35/12206/357766/5e43b59cE5a7aa4dd/0753be765166c195.jpg',
						money: '175.78',
					},
					{
						title: '钟薛高 钟意你系列 特牛乳*4片 丝绒可可*4片 半巧主义*2 冰淇淋生鲜雪糕 10片装',
						img: '/static/images/goods/there.jpg',
						goods_id: 203,
						money: '152.00 ',
						sku: '',
						number: 1,
					},
				],
				isShow: true,
				colors: ""
			};
		},
		components: {
			tabs,
			loading,
			navBar
		},
		props: {},

		/**
		 * 生命周期函数--监听页面加载
		 */
		onLoad: function(options) {
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
		onReady: function() {},

		/**
		 * 生命周期函数--监听页面显示
		 */
		onShow: function() {},

		/**
		 * 生命周期函数--监听页面隐藏
		 */
		onHide: function() {},

		/**
		 * 生命周期函数--监听页面卸载
		 */
		onUnload: function() {},

		/**
		 * 页面相关事件处理函数--监听用户下拉动作
		 */
		onPullDownRefresh: function() {},

		/**
		 * 页面上拉触底事件的处理函数
		 */
		onReachBottom: function() {},

		/**
		 * 用户点击右上角分享
		 */
		onShareAppMessage: function() {},
		methods: {
			setTabs(item, index) {
				this.setData({
					active: item.id
				});
			},
			onafterSale(item) { //申请售后
				uni.navigateTo({
					url: '/pages/views/order/afterSale?goodData=' + JSON.stringify(item)
				})
			},
			onwithdraw() { //取消申请
				uni.showModal({
					title: '确认要取消该售后申请吗?',
					confirmColor: this.colors,
					success: (res) => {
						if (res.confirm) {
							console.log('取消成功')
						}
					}
				})
			},
			ongetMoreList() { //上拉获取更多商品列表
				console.log('触发到底事件')
			}
		}
	};
</script>
<style scoped lang="scss">
	.order_list {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background-color: #F8F8F8;
	}

	.list_box {
		padding: 0 3%;
		box-sizing: border-box;
		height: calc(100vh - 160upx);
		overflow: hidden;
		/* #ifdef MP */
		padding-bottom: 50upx;
		/* #endif */
		width: 100%;
		padding-top: 20upx;
	}

	.lists {
		margin: 0 auto;
		padding: 20upx;
		margin-top: 5upx;
		background-color: #ffffff;
		border-radius: 10upx;
		margin-bottom: 20upx;
		box-shadow: 0upx 0upx 10upx #EDEDED;
		box-sizing: border-box;
		width: 98%;
	}

	.type {
		height: 50upx;
		font-size: 26upx;
		line-height: 50upx;
		font-weight: bold;
		text-align: right;
		display: flex;
		justify-content: space-between;
	}

	.type .order_id {
		font-weight: 600;
		color: #999;
		font-size: 24upx;
	}

	.lists .top {
		margin-bottom: 20upx;

		.top_box {
			display: flex;
			align-content: center;
		}

		&:last-of-type {
			margin-bottom: 0;
		}
	}

	.lists .top .cover {
		min-width: 180upx;
		max-width: 180upx;
		height: 180upx;
		display: block;
		border-radius: 10upx;
	}

	.lists .top .top_right {
		margin-left: 20upx;
		width: 100%;
	}

	.lists .top .order_name {
		line-height: 40upx;
		height: 80upx;
		font-size: 26upx;
		font-weight: bold;
		color: #333;
		display: -webkit-box;
		-webkit-box-orient: vertical;
		-webkit-line-clamp: 2;
		overflow: hidden;
	}

	.lists .top .sku {
		height: 40upx;
		line-height: 40upx;
		font-size: 24upx;
		border-radius: 8upx;
		padding: 0 10upx;
		color: #A7A7A7;
		background-color: #F7F7F7;
	}

	.lists .top .price {
		width: 100%;
		display: flex;
		align-items: center;
		height: 40upx;
		line-height: 40upx;
		margin-top: 20upx;
		position: relative;
	}

	.bottom {
		display: flex;
		justify-content: flex-end;
		align-items: center;
		margin-top: 10upx;
		margin-bottom: 10upx;

		view {
			padding: 8upx 15upx;
			border-radius: 8upx;
			text-align: center;
			font-size: 24upx;
			border: 1upx solid #eee;
			overflow: hidden;
		}

		.success {
			border: none;
			width: 100%;
			background-color: #F7F7F7;
			height: 80upx;
			line-height: 80upx;
			display: flex;
			align-items: center;
			margin-top: 10upx;
			justify-content: space-between;
			.success_type {
				margin-right: 20upx;
				width: 60upx;
			}
			.success_t2 {
				color: #999;
				transform: translateX(-100upx);
			}
			.right_img{
				width: 40upx;
				height: 40upx;
				float: right;

			}
			&:active{
				opacity: 0.8;
			}
		}
	}

	.price .t1 {
		font-size: 30upx;
		display: block;
		color: $mycolor;
	}

	.price .t2 {
		font-size: 24upx;
		margin-left: 15upx;
		display: block;
		color: #C5C5C5;
		text-decoration: line-through;
	}

	.price .t3 {
		float: right;
		font-weight: bold;
		font-size: 28upx;
		color: #999;
		position: absolute;
		right: 0upx;
		top: 0;
	}

	.nodata {
		color: #ccc;
		text-align: center;
		font-size: 24upx;
		margin-bottom: 30upx;
		height: 80upx;
		line-height: 80upx;
	}
</style>
