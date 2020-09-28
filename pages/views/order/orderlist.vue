<template>
	<view>
		<view class="order_list">
			<view style="background-color: #FFFFFF;">
				<navBar :showLeft="true" navTitle="订单列表" textcolor="#000" :leftBg="false"></navBar>
			</view>
			<!-- 顶部tab -->
			<view class="nav_top" style="border-bottom: 1upx solid #F8F8F8;">
				<tabs :colors="colors" :tabList="tabList" :active="active" @setTabs="setTabs"></tabs>
			</view>
			<scroll-view class="list_box" :scroll-y="true" @scrolltolower="ongetMoreList">
				<view v-for="(item, index) in orderList" :key="index" class="lists">
					<view class="type">
						<text class="order_id">订单编号:{{item.order_No}}</text>
						<text class="order_type" :style="'color:' + colors">
							{{active | setStatus}}
						</text>
					</view>
					<view v-for="(row, index2) in item.goods" :key="index2" class="top" @tap="jumpDetails(item)">
						<image :src="row.img" mode="aspectFill"></image>
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
						<view class="address">店铺地址：北京市海淀区苏家坨乡前沙涧村</view>
						<view class="btns">
							<block v-if="active == 0">
								<view class="pay" :style="'color:#fff;background:' + colors+ ';border-color:' + colors" @tap="jumpDetails(item)">去付款</view>
								<view class="pay shouhou" :style="'color:' + colors + ';border-color:' + colors" @tap="cencalOrder(item)">取消订单</view>	
							</block>
							<block v-if="active == 1">
								<view class="pay shouhou"  @tap="onRefund(item)">申请退款</view>
							</block>
							<block v-if="active == 2">
								<view class="pay" :style="'color:#fff;background:' + colors+ ';border-color:' + colors">确认收货</view>
								<view class="pay shouhou" @tap="onRefund(item)">申请退款</view>
							</block>
							<block v-if="active == 3">
								<view class="pay shouhou" :style="'color:' + colors + ';border-color:' + colors" @tap="jumpDetails(item)">订单评价</view>
								<view class="pay shouhou" @tap="jumpDetails(item)">申请售后</view>
							</block>
							<view class="pay shouhou" v-if="active == 4">删除订单</view>
						</view>
					</view>
				</view>
				<view class="nodata" v-if="orderList.length >= 3">—— 到底啦 ——</view>
				<nodata :colors="colors" title="暂无订单信息" v-if="orderList.length == 0"></nodata>
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
					name: '待付款',
					id: 0
				}, {
					name: '待发货',
					id: 1
				}, {
					name: '待收货',
					id: 2
				},{
					name: '待评价',
					id: 3
				},
				{
					name: '已完成',
					id: 4
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
				isShow: true,
				colors: ""
			};
		},
		filters: {
			setStatus(value) {
				if (value == 0) {
					return '待付款'
				} else if (value == 1) {
					return '待发货'
				} else if (value == 2) {
					return '待收货'
				} else if (value == 3) {
					return '待评价'
				} else if (value == 4) {
					return '已完成'
				}
			}
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
			if (options.tabIndex) {
				this.setData({
					active: Number(options.tabIndex)
				});
			}
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

			jumpDetails() { //模拟跳转商品详情
				uni.navigateTo({
					url: '/pages/views/order/orderdetails?status=' + this.active
				});
			},
			cencalOrder(item) {
				//取消订单
				uni.showModal({
					title:'确认要取消该订单吗?',
					confirmColor:this.colors,
					success: (res) => {
						if(res.confirm){
							console.log('取消成功')
						}
					}
				})
			},
			onRefund(item){
				// 申请退款
				uni.navigateTo({
					url: '/pages/views/order/cancelorder?orderId'
				});
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
		display: flex;
		align-content: center;
		margin-bottom: 20upx;
	}

	.lists .top image {
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

	.price .t1 {
		font-size: 30upx;
		// font-weight: bold;
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

	.list_box .bottom {
		margin-top: 20upx;
	}

	.list_box .bottom .address {
		font-size: 24upx;
		color: #696969;
	}

	.list_box .bottom .btns {
		margin-top: 15upx;
		overflow: hidden;
	}

	.list_box .bottom .btns .pay {
		padding: 8upx 15upx;
		border-radius: 8upx;
		text-align: center;
		font-size: 24upx;
		float: right;
		margin-left: 20upx;
		box-sizing: border-box;
		border: 1upx solid #ddd;
	}

	.pay:active {
		opacity: .8;
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
