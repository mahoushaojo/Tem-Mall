<template>
	<view class="cart">
		<block v-if="cartList.length !== 0">
			<view class="clearCart" :style="'color:' + colors" @click="clearInvalid">清空失效商品</view>
			<view class="cart_box">
				<view v-for="(item, index) in cartList" :key="index" class="cart_list" @longpress.stop="onshowDel(item,index)"
				 @touchend="ontouchend">
					<view class="checkbox-box" @tap="setCurrent(item,index)">
						<view class="checkbox" :style="'border-color:' + colors" v-if="item.status !== 1 ">
							<view :class="item.current == true ? 'on':''" :style="'background-color:' + colors"></view>
						</view>
						<view class="checkbox" style="border-color:#ccc" v-else>
							<view></view>
						</view>
					</view>
					<view class="cover" @tap="jumpDetails(item,index)">
						<image :src="item.selectSku.img" mode="aspectFill" v-if="item.selectSku"></image>
						<image :src="item.img" mode="aspectFill" v-else></image>
						<text class="masks"></text>
						<text class="mask" v-if="item.status == 1">已失效</text>
					</view>
					<view class="right">
						<view class="goods_name" @tap="jumpDetails(item,index)">
							{{item.title}}
						</view>
						<view class="sku">
							<block v-if="item.selectSku">
								<text style="margin-left: 10upx;">{{item.selectSku.goods_sku_text}}</text>
							</block>
							<block v-else>
								<text>暂无规格</text>
							</block>
						</view>
						<view class="numbers">
							<text class="price" v-if="item.selectSku">
								￥{{(Number(item.selectSku.money) * item.number).toFixed(2)}}
							</text>
							<text class="price" v-else>
								￥{{(Number(item.money) * item.number).toFixed(2)}}
							</text>
							<view class="right_btn">
								<view class="sub" @tap="onsub(item,index)" :style="'color:' + (item.num == 1?'#ccc':'')">-</view>
								<view class="input">
									<input :value="item.number" maxlength="2" disabled></input>
								</view>
								<view class="add" @tap="onadd(item,index)">+</view>
							</view>
						</view>
					</view>
					<!-- 删除的遮罩层 长按触发 -->
					<view class="del_mask" v-if="current == index" :style="'z-index:' + (current == index ?'99':'-21')" @tap="oncencal">

					</view>
					<view class="dask_del" :style="'opacity:' + (current == index ?'1':'0') + ';z-index:' + (current == index ?'100':'-20')+';left:'+(current == index?'0':'-100%')">
						<text class="del" @tap="delItem(item,index)">删除</text>
						<text class="cencal" @tap="oncencal">取消</text>
					</view>
				</view>
				<view class="daodi" v-if="cartList.length >= 5">—— 到底啦 ——</view>
			</view>
			<!-- 全选 -->
			<view class="bottom_all" :style="{marginBottom: bottomShow}">
				<view class="left">
					<view class="checkbox-box" @tap="setAllCurrent">
						<view class="checkbox" :style="'border-color:' + colors">
							<view :class="allCurrent == true ? 'on':''" :style="'background-color:' + colors"></view>
						</view>
						<view class="text">全选</view>
					</view>
					<view class="delAll" @click="delectAll" :style="'border-color:' + colors + ';color:' + colors" v-if="allCurrent == true">删除</view>
				</view>
				<view class="rights">
					<view class="jiesuan" :style="'background-color:' + colors" @click="settlement">结算({{sum}})</view>
					<view class="sum">合计：<text style="font-size: 30upx;">￥{{sumPrice || 0}}</text></view>
				</view>
			</view>
		</block>
		<!-- 如果购物车没有数据 -->
		<view class="nocart" v-if="cartList.length == 0">
			<text class="iconfont icon-gouwuche1" :style="'color:' + colors"></text>
			<view>空空如也,<text :style="'color:' + colors" @tap="onStroll">随便逛逛 ></text></view>
		</view>
	</view>
</template>

<script>
	var app = getApp();
	import {
		getCart,
		setGoodsData,
		removeCart
	} from '@/utils/auth.js'
	export default {
		data() {
			return {
				colors: '',
				cartList: [],
				current: 99999,
				allCurrent: false,
				sum: 0,
				sumPrice: 0,
				lock: false,
				bottomShow: ''
			};
		},

		components: {},
		props: {},

		/**
		 * 生命周期函数--监听页面加载
		 */
		onLoad: function(options) {
			// #ifdef MP
			this.bottomShow = 0
			// #endif
			// #ifdef H5
			this.bottomShow = '100upx'
			// #endif
		},

		/**
		 * 生命周期函数--监听页面初次渲染完成
		 */
		onReady: function() {},

		/**
		 * 生命周期函数--监听页面显示
		 */
		onShow: function() {
			let cart = getCart() || []
			this.setData({
				colors: app.globalData.newColor,
				current: '99999',
				cartList: cart,
				allCurrent: false,
				sum: 0,
				sumPrice: 0
			});
		},

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
			setCurrent(item, index) {
				if (item.status == 1) { //如果商品的status 状态为1 说明已经失效
					return
				}
				//单选
				let cartList = this.cartList;
				let dataList = this.cartList[index];
				dataList.current = !dataList.current;
				let datas = 'cartList[' + index + ']';
				let currentAll = false;
				let count = [];
				cartList.forEach(e => {
					if (e.current == true) {
						count.push(e);
					}
				});
				if (count.length == cartList.length) { //判断是否全选
					currentAll = true;
				}
				this.setData({
					[datas]: dataList,
					allCurrent: currentAll,
				});
				this.getSumprice()
			},

			onshowDel(item, index) {
				//显示删除
				this.setData({
					current: index,
					lock: true
				});
				return;
			},

			delItem(item, index) {
				//点击删除 模拟删除本地数据
				this.cartList.splice(index, 1)
				this.setData({
					current: '9999',
					lock: false
				})
				uni.showToast({
					title: '删除成功',
					icon: 'none'
				})
				this.setTabBarBadge()
			},

			oncencal() {
				//点击取消
				this.setData({
					current: '99999'
				});
			},

			onsub(item, index) {
				//减少 //已失效商品不做操作
				if (item.status == 1 || item.number <= 1) {
					return
				}
				let number = item.number - 1
				this.$set(item, 'number', number)
				this.getSumprice() //计算总价
			},

			onadd(item, index) {
				if (item.status == 1) { //已失效商品不做操作
					return
				}
				//增加
				let num = item.number + 1;
				this.$set(item, 'number', num)
				this.getSumprice() //计算总价
			},
			setAllCurrent() {
				//设置全选
				let that = this
				let dataList = that.cartList;
				let allCurrent = that.allCurrent;
				let sumPrice = '';
				// 先判断是否存在失效商品 必须清楚失效商品之后才能继续全选
				let status = true
				dataList.forEach(e => {
					if (e.status == 1) {
						uni.showToast({
							title: '存在失效商品',
							icon: 'none'
						})
						status = false
					}
				})
				if (status == false) {
					return
				}
				dataList.forEach(element => { //反选
					element.current = !allCurrent;
				});
				allCurrent = !allCurrent;
				that.setData({
					cartList: dataList,
					allCurrent: allCurrent,
				});
				that.getSumprice()
			},
			getSumprice() { //计算总价
				let that = this
				let dataList = that.cartList;
				let sumPrice = '';
				let count = []
				dataList.forEach(e => {
					if (e.current == true) {
						count.push(e);
					}
				});
				let length = count.length;
				for (var i = 0; i < length; i++) { //计算总价
					var data = count[i];
					if (data.selectSku) {
						sumPrice = (Number(sumPrice) + Number(data.selectSku.money * data.number)).toFixed(2)
					} else {
						sumPrice = (Number(sumPrice) + Number(data.money * data.number)).toFixed(2)
					}
				}
				that.sum = length
				that.sumPrice = sumPrice
			},
			ontouchend() { //隐藏删除弹窗
				console.log('触发了touch');
				if (this.lock) {
					setTimeout(() => {
						this.setData({
							lock: false
						});
					}, 100);
				}
			},
			clearInvalid() { //模拟清空失效商品  根据商品的status值来判断商品状态
				this.cartList.forEach((e, index) => {
					if (e.status == 1) {
						this.cartList.splice(index, 1)
					}
					e.current = false
				})
				uni.showToast({
					title: '清空成功~',
					icon: 'none'
				})
				this.setTabBarBadge()
			},
			delectAll() { //模拟删除所有商品
				this.cartList = []
				removeCart()
				this.setTabBarBadge()
			},
			setTabBarBadge() { //设置购物车角标
				let length = String(this.cartList.length)
				if (length == 0) {
					uni.removeTabBarBadge({
						index: 2
					})
					return
				}
				uni.setTabBarBadge({ //重新设置角标
					//给tabBar添加角标
					index: 2,
					text: length
				});
			},
			jumpDetails(item, index) {
				if (item.status == 1 || this.lock) { //已失效商品不做操作
					return
				}
				uni.navigateTo({
					url: '/pages/views/goods/goodsDetails'
				});
			},
			settlement() { //结算
				if (this.sum == 0) {
					return
				}
				// 计算出被选中的数据
				uni.showLoading({
					title: '提交中...'
				})
				let arr = []
				this.cartList.forEach(e => {
					if (e.current == true) {
						arr.push(e)
					}
				})
				setGoodsData(arr)  //存储商品信息去支付
				setTimeout(() => {
					uni.hideLoading()
					uni.navigateTo({
						url: '/pages/views/order/confirmOrder'
					})
				}, 1000)
			},
			onStroll() { //随便逛逛
				uni.switchTab({
					url: '/pages/views/tabBar/category'
				});
			}

		}
	};
</script>
<style lang="scss" scoped>
	.cart {
		padding: 10rpx 4%;
	}

	.clearCart {
		height: 80rpx;
		font-size: 26rpx;
		text-align: right;
		line-height: 80rpx;
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		padding: 0 4%;
		background-color: #FFFFFF;
		z-index: 100;
	}

	.cart_box {
		margin-top: 80rpx;
		padding-bottom: 100rpx;
	}

	.cart_list {
		width: 92vw;
		height: calc(22vw + 34rpx);
		border-radius: 12rpx;
		box-shadow: 0px 4rpx 16rpx rgba(0, 0, 0, .1);
		overflow: hidden;
		border: 0;
		display: flex;
		align-items: center;
		position: relative;
		margin-bottom: 20rpx;
	}

	.cart_list .checkbox-box {
		padding-left: 16rpx;
		flex-shrink: 0;
		height: 22vw;
		margin-right: 16rpx;
		align-items: center;
		position: relative;
		width: 50rpx;
	}

	.cart_list .checkbox-box .checkbox {
		width: 28rpx;
		height: 28rpx;
		border-radius: 100%;
		border: solid 1rpx;
		position: absolute;
		top: 50%;
		left: 20rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		transform: translateY(-50%);
	}

	.cart_list .checkbox-box .checkbox .on {
		width: 20rpx;
		height: 20rpx;
		border-radius: 100%;
		align-items: center;
	}

	.cart_list .cover {
		width: 22vw;
		height: 22vw;
		flex-shrink: 0;
		margin-left: 10rpx;
		border-radius: 8rpx;
		overflow: hidden;
		margin-right: 10rpx;
		position: relative;
	}

	.cart_list .cover .mask {
		width: 100%;
		height: 100%;
		background-color: rgba(0, 0, 0, .6);
		position: absolute;
		top: 0;
		left: 0;
		text-align: center;
		color: #ddd;
		font-size: 24upx;
		line-height: 22vw;
	}

	.cart_list .cover .masks {
		width: 100%;
		height: 100%;
		position: absolute;
		top: 0;
		left: 0;
	}

	.cart_list .cover image {
		width: 100%;
		height: 100%;
		display: block;
	}

	.daodi {
		text-align: center;
		font-size: 24rpx;
		color: #ccc;
		margin-top: 30rpx;
	}

	.right {
		height: 22vw;
		width: 100%;
		/* overflow: hidden; */
		display: flex;
		flex-wrap: wrap;
		padding-right: 15rpx;
		position: relative;
	}

	.right .goods_name {
		width: 100%;
		font-size: 24rpx;
		line-height: 34rpx;
		max-height: 68rpx;
		color: #333;
		overflow: hidden;
		text-overflow: ellipsis;
		display: -webkit-box;
		-webkit-line-clamp: 2;
		-webkit-box-orient: vertical;
	}

	.sku {
		font-size: 22rpx;
		background-color: #f3f3f3;
		border-radius: 10rpx;
		height: 40rpx;
		line-height: 40rpx;
		padding: 0 10rpx;
		color: #a7a7a7;
		margin-bottom: 40rpx;
	}

	.numbers {
		position: absolute;
		width: 100%;
		overflow: hidden;
		display: flex;
		justify-content: space-between;
		align-items: flex-end;
		height: 50rpx;
		bottom: -5rpx;
	}

	.numbers .price {
		font-size: 30rpx;
		line-height: 50rpx;
		color: $mycolor;
	}

	.numbers .right_btn {
		display: flex;
		justify-content: center;
		align-items: flex-end;
		margin-right: 20rpx;
	}

	.right_btn .sub {
		width: 40rpx;
		height: 40rpx;
		font-size: 40rpx;
		background-color: #f3f3f3;
		border-radius: 4rpx;
		text-align: center;
		line-height: 40rpx;
	}

	.right_btn .sub:active {
		background-color: #f8f8f8;
	}

	.right_btn .input {
		width: 50rpx;
		height: 50rpx;
		margin: 0 8rpx;
		background-color: #f3f3f3;
	}

	.right_btn .input input {
		width: 50rpx;
		height: 50rpx;
		display: flex;
		font-size: 22rpx;
		text-align: center;
		align-items: center;
		justify-content: center;
		color: $mycolor;
	}

	.right_btn .add {
		width: 40rpx;
		height: 40rpx;
		font-size: 40rpx;
		background-color: #f3f3f3;
		border-radius: 4rpx;
		text-align: center;
		line-height: 40rpx;
	}

	.right_btn .add:active {
		background-color: #f8f8f8;
	}

	.del_mask {
		width: 100%;
		height: 100%;
		position: fixed;
		top: 0;
		left: 0;
		z-index: -21;
	}

	.dask_del {
		width: 100%;
		height: 100%;
		position: absolute;
		top: 0;
		left: 0;
		background-color: rgba(0, 0, 0, .5);
		z-index: -20;
		display: flex;
		align-items: center;
		opacity: 0;
		transition: all 0.3s;
	}

	.dask_del .del,
	.cencal {
		width: 100rpx;
		height: 100rpx;
		display: flex;
		justify-content: center;
		line-height: 100rpx;
		border-radius: 100%;
		text-align: center;
		font-size: 24rpx;
		background: linear-gradient(#FF5D39, #FFAF48);
		color: #ffffff;
		font-weight: 500;
		margin: 0 auto;
	}

	.dask_del text:active {
		opacity: 0.9;
	}

	.cencal {
		background: linear-gradient(#FFE846, #FFCD43);
	}

	.bottom_all {
		height: 100rpx;
		width: 100%;
		padding: 0 2%;
		align-items: center;
		justify-content: center;
		position: fixed;
		bottom: 0;
		left: 0;
		background-color: #fdfdfd;
		z-index: 11;
		border-top: 1upx solid #F8F8F8;
	}

	.bottom_all .left {
		float: left;
		width: 40vw;
		font-size: 24rpx;
		position: relative;
	}

	.bottom_all .left .delAll {
		height: 40rpx;
		border-radius: 20rpx;
		font-size: 22rpx;
		line-height: 38rpx;
		border: solid 1rpx;
		text-align: center;
		width: 92rpx;
		position: absolute;
		right: 50rpx;
		top: 50%;
		transform: translateY(-50%);
	}

	.bottom_all .left .text {
		position: absolute;
		left: 60rpx;
		top: 50%;
		transform: translateY(-52%);
	}

	.bottom_all .checkbox-box {
		padding-left: 16rpx;
		flex-shrink: 0;
		height: 100rpx;
		margin-right: 16rpx;
		align-items: center;
		position: relative;
	}

	.bottom_all .checkbox-box .checkbox {
		width: 32rpx;
		height: 32rpx;
		border-radius: 100%;
		border: solid 1rpx;
		position: absolute;
		top: 50%;
		left: 20rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		transform: translateY(-50%);
	}

	.bottom_all .checkbox-box .checkbox .on {
		width: 20rpx;
		height: 20rpx;
		border-radius: 100%;
		align-items: center;
	}

	.bottom_all .rights {
		float: right;
		width: 56vw;
		line-height: 100rpx;
		font-size: 24rpx;
	}

	.bottom_all .rights view {
		float: right;
	}

	.bottom_all .rights .sum {
		font-weight: bold;
		margin-right: 20rpx;
	}

	.bottom_all .rights .jiesuan {
		padding: 0 22rpx;
		border-radius: 24rpx;
		background-color: pink;
		height: 50rpx;
		line-height: 50rpx;
		color: #ffffff;
		font-size: 24rpx;
		margin-top: 28rpx;
		margin-left: 40rpx;

	}

	/* 购物车为空的样式} */
	.nocart {
		text-align: center;
		margin-top: 30vh;
	}

	.nocart .iconfont {
		font-size: 80rpx;
		text-align: center;
	}

	.nocart view {
		height: 40rpx;
		line-height: 40rpx;
		font-size: 24rpx;
		color: #999;
		margin-top: 20rpx;
	}
</style>
