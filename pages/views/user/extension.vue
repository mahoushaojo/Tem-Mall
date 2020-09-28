<template>
	<!-- 推广海报 -->
	<view>
		<view class="myextension">
			<canvas class="mycanvas" canvas-id="mycanvas" :style="'width:' + (windowWidth-30) + 'px;height:560px'"></canvas>
			<!-- #ifdef H5 -->
			<block>
				<image class="imgs" :style="'width:' + (windowWidth-30) + 'px;height:560px'" :src="imgUrl" mode=""></image>
			</block>
			<!-- #endif -->
			<!-- #ifdef MP -->
			<view class="saveImg" @tap="saveImg" :style="'background:' + colors">保存海报</view>
			<!-- #endif -->
			<!-- #ifdef H5 -->
			<p class="tips">长按图片进行保存</p>
			<!-- #endif -->
		</view>
		<loading v-if="isShow == true"></loading>
	</view>
</template>

<script>
	var app = getApp();
	import loading from "../../commponent/public/loading";
	export default {
		data() {
			return {
				isShow: true,
				colors: '',
				windowHeight: '',
				windowWidth: '',
				imgUrl: ''
			};
		},

		components: {
			loading
		},
		props: {},

		/**
		 * 生命周期函数--监听页面加载
		 */
		onLoad: function(options) {
			this.setData({
				colors: app.globalData.newColor
			});
			this.getSystem()
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
			getSystem() {
				let that = this;
				uni.getSystemInfo({
					success: function(res) {
						that.setData({
							windowHeight: res.windowHeight,
							windowWidth: res.windowWidth
						});
						that.createPoster()
					}
				});
			},
			createPoster() { //生成海报
				var ctx = uni.createCanvasContext('mycanvas', this)
				ctx.fillStyle = '#FFFFFF'
				ctx.fillRect(0, 0, this.windowWidth - 30, 560)
				//绘制背景图片
				ctx.drawImage('/static/images/user/poster.jpg', 0, 0, this.windowWidth - 30, 560)
				ctx.save()
				// 绘制圆角二维码
				let avatar_width = 60; //头像宽度
				let avatar_height = 60; //头像高度
				let avatar_x = 15; //头像的x坐标
				let avatar_y = 15; //头像的y坐标
				let radius = 8 //头像的圆角弧度
				this.setRadius(ctx, avatar_width, avatar_height, avatar_x, avatar_y, radius)
				// #ifdef MP
				setTimeout(() => { //必须延时执行 不然h5不显示
					ctx.save();
					ctx.draw()
				}, 200)
				// #endif
				// #ifdef H5
				setTimeout(() => { //必须延时执行 不然h5不显示
					ctx.save();
					ctx.draw(true, () => {
						uni.canvasToTempFilePath({
							canvasId: 'mycanvas',
							success: (res) => {
								this.imgUrl = res.tempFilePath
							}
						})
					})
				}, 500)
				// #endif
			},
			setRadius(ctx, avatar_width, avatar_height, avatar_x, avatar_y, radius) {
				/**
				 * 绘制圆角
				 */
				ctx.arc(avatar_x + radius, avatar_y + radius, radius, Math.PI, Math.PI * 3 / 2);
				ctx.lineTo(avatar_width - radius + avatar_x, avatar_y);
				ctx.arc(avatar_width - radius + avatar_x, radius + avatar_y, radius, Math.PI * 3 / 2, Math.PI * 2);
				ctx.lineTo(avatar_width + avatar_x, avatar_height + avatar_y - radius);
				ctx.arc(avatar_width - radius + avatar_x, avatar_height - radius + avatar_y, radius, 0, Math.PI * 1 / 2);
				ctx.lineTo(radius + avatar_x, avatar_height + avatar_y);
				ctx.arc(radius + avatar_x, avatar_height - radius + avatar_y, radius, Math.PI * 1 / 2, Math.PI);
				// 开始填充
				ctx.strokeStyle = "#fff";
				ctx.fill() //保证图片无bug填充
				ctx.clip(); //画了圆 再剪切  原始画布中剪切任意形状和尺寸。一旦剪切了某个区域，则所有之后的绘图都会被限制在被剪切的区域内

				ctx.drawImage('/static/images/ewm.png', avatar_x, avatar_y, avatar_width, avatar_height);
				ctx.closePath()
				ctx.restore();
			},
			saveImg() {
				//保存图片
				uni.showLoading({
					title: '保存中...'
				});
				let that = this;
				uni.getSetting({
					success(res) {
						if (!res.authSetting['scope.writePhotosAlbum']) {
							uni.hideLoading();
							uni.openSetting({
								success(res) { //拒绝授权后重新提示授权，并授权成功
								}
							});
						} else {
							setTimeout(() => {
								uni.canvasToTempFilePath({
									canvasId: 'mycanvas',
									success: function(res) {
										console.log(res);
										uni.saveImageToPhotosAlbum({
											filePath: res.tempFilePath,
											success(re) {
												uni.hideLoading();
												uni.showToast({
													title: '保存成功',
													icon: 'success'
												});
											},
											fail(err) {
												uni.showToast({
													title: '保存失败',
													icon: 'none'
												});
											}

										});
									}
								});
							}, 1000);
						}
					}

				});
			}
		}
	};
</script>
<style lang="scss" scoped>
	.myextension {
		background-color: #FFFFFF;
		overflow: hidden;
		padding-bottom: 100upx;
	}

	.myextension image {
		width: 100%;
		height: 100%;
		display: block;
	}

	.saveImg {
		width: 300upx;
		height: 80upx;
		border-radius: 40upx;
		box-shadow: 0 10upx 10upx #ddd;
		background-color: pink;
		line-height: 80upx;
		text-align: center;
		color: #fff;
		margin: 0 auto;
		margin-top: 60upx;
	}

	.mycanvas {
		margin: 0 auto;
		transform: translateY(30upx);
		border-radius: 10upx;
		overflow: hidden;
	}

	.imgs {
		position: absolute;
		top: 30upx;
		left: 50%;
		transform: translateX(-50%);
		border-radius: 10upx;
		overflow: hidden;
	}
	.tips{
		text-align: center;
		color: #999;
		font-size: 24upx;
		margin-top: 50upx;
	}
</style>
