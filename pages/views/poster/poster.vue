<template>
	<view>
		<canvas class="mycanvas" canvas-id="mycanvas" :style="'width:' + (windowWidth-60) + 'px;height:520px'"></canvas>
		<!-- #ifdef H5 -->
		<block>
			<image class="imgs" :style="'width:' + (windowWidth-60) + 'px;height:520px'" :src="imgUrl" mode=""></image>
		</block>
		<!-- #endif -->
		<!-- #ifdef MP -->
		<view class="saveImg" v-if="shows == true" @tap="saveImg" :style="'background:' + colors">
			保存图片
		</view>
		<!-- #endif -->
		<!-- #ifdef H5 -->
		<p class="tips"  v-if="shows == true">长按图片进行保存</p>
		<!-- #endif -->
		<loading v-if="isShow == true"></loading>
	</view>
</template>

<script>
	var app = getApp();
	import loading from "../../commponent/public/loading";
	export default {
		data() {
			return {
				windowWidth: '',
				windowHeight: '',
				isShow: true,
				colors: "",
				ctx: '',
				imgUrl: '',
				shows:false,
				userInfo: {
					name: '反转',
					logo: '/static/images/face.jpg',
					goodsImg: '/static/images/goods/one.jpg',
					price: '3599.00',
					goodsName: '魅族17 8GB+128GB 十七度灰 骁龙865 旗舰5G手机 30W快充 6400W后置主摄 90Hz屏幕 支持NFC 智能拍照游戏手机'
				}
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
			this.getSystem();
			this.setData({
				colors: app.globalData.newColor
			});
			this.setPoster();
			setTimeout(() => {
				this.setData({
					isShow: false
				});
			}, 600);
		},

		/**
		 * 生命周期函数--监听页面初次渲染完成
		 */
		onReady: function() {

		},

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
					}
				});
			},

			setPoster() {
				let ctx = uni.createCanvasContext('mycanvas', this); // 绘制背景
				ctx.fillStyle = "#FFFFFF"
				ctx.fillRect(0, 0, this.windowWidth - 60, 520)
				/**
				 * 绘制名称
				 */
				const setText = (context, fs, color, x, y, c, bold) => {
					context.setFillStyle(color);
					context.setTextAlign('left');
					if (bold) {
						context.font = 'normal bold 20px Arial,sans-serif';
					} else {
						context.font = 'normal 20px Arial,sans-serif';
					}
					context.setFontSize(fs);
					context.fillText(c, x, y);
					context.restore();
				};
				setText(ctx, 14, '#333', 85, 35, this.userInfo.name, 'bold');
				setText(ctx, 12, '#999', 85, 58, '为您挑选了一个好礼物');
				ctx.save();
				/**
				 * 绘制头像
				 */
				ctx.beginPath();
				let avatar_width = 60; //头像宽度
				let avatar_height = 60; //头像高度
				let avatar_x = 15; //头像的x坐标
				let avatar_y = 15; //头像的y坐标
				let radius = 8 //头像的圆角弧度
				// 绘制圆角头像
				this.setRadius(ctx, avatar_width, avatar_height, avatar_x, avatar_y, radius)
				// 绘制圆形图片
				// this.setCircular(ctx, avatar_width, avatar_height, avatar_x, avatar_y)

				// 绘制商品图片
				this.setGoodsImg(ctx)

				// 绘制商品价格
				let pirce = '￥ ' + this.userInfo.price
				this.setGoodsPrice(ctx, 20, this.colors, 15, 410, pirce)

				// 绘制商品名称
				this.setGoodsName(ctx)
				// 绘制二维码
				this.setEwm(ctx);
			},

			setEwm(ctx) {
				console.log('生成二维码')
				let code_widht = 100; //二维码宽度
				let code_height = 100; //二维码高度
				let x = this.windowWidth -170
				ctx.drawImage("/static/images/ewm.png", x, 410, code_widht, code_height);
				// #ifdef MP
				setTimeout(() => { //必须延时执行 不然h5不显示
					ctx.save();
					ctx.draw()
					this.shows = true
				}, 200)
				// #endif
				// #ifdef H5
				setTimeout(() => { //必须延时执行 不然h5不显示
					ctx.save();
					ctx.draw(true,()=>{
						uni.canvasToTempFilePath({
							canvasId: 'mycanvas',
							success: (res) => {
								console.log(res)
								this.imgUrl = res.tempFilePath
							}
						})
					})
					this.shows = true
				}, 200)
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

				ctx.drawImage(this.userInfo.logo, avatar_x, avatar_y, avatar_width, avatar_height);
				ctx.closePath()
				ctx.restore();
			},
			setCircular(ctx, avatar_width, avatar_height, avatar_x, avatar_y) { //绘制圆形图片
				/**
				 * 绘制圆形
				 */
				//先画个圆   前两个参数确定了圆心 （x,y） 坐标  第三个参数是圆的半径  四参数是绘图方向  默认是false，即顺时针
				ctx.arc(avatar_width / 2 + avatar_x, avatar_height / 2 + avatar_y, avatar_width / 2, 0, Math.PI * 2, false); //画圆
				// 开始填充
				ctx.strokeStyle = "#fff";
				ctx.fill() //保证图片无bug填充
				ctx.clip(); //画了圆 再剪切  原始画布中剪切任意形状和尺寸。一旦剪切了某个区域，则所有之后的绘图都会被限制在被剪切的区域内

				ctx.drawImage(this.userInfo.logo, avatar_x, avatar_y, avatar_width, avatar_height);
				ctx.closePath()
				ctx.restore();
			},
			setGoodsImg(ctx) { //绘制中间商品图片
				let width = this.windowWidth - 90
				ctx.drawImage(this.userInfo.goodsImg, 15, 95, width, width);
				ctx.save();
			},
			setGoodsPrice(ctx, fs, color, x, y, c, bold) { //绘制商品价格
				ctx.setFillStyle(color);
				ctx.setTextAlign('left');
				if (bold) {
					ctx.font = 'normal bold 20px Arial,sans-serif';
				} else {
					ctx.font = 'normal 20px Arial,sans-serif';
				}
				ctx.setFontSize(fs);
				ctx.fillText(c, x, y);
				ctx.restore();
				ctx.save();
			},
			setGoodsName(ctx) { //绘制商品名称
				let obj = {
					x: 20, //绘制文本的左上角x坐标位置
					y: 440, //绘制文本的左上角y坐标位置
					width: 240,
					height: 20,
					line: 2,
					color: '#202020',
					size: 14, //字体的字号
					align: 'left',
					baseline: 'top',
					text: this.userInfo.goodsName,
					bold: true
				};
				var td = Math.ceil(obj.width / (obj.size));
				var tr = Math.ceil(obj.text.length / td);
				for (var i = 0; i < tr; i++) {
					var txt = {
						x: obj.x,
						y: obj.y + (i * obj.height),
						color: obj.color,
						size: obj.size,
						align: obj.align,
						baseline: obj.baseline,
						text: obj.text.substring(i * td, (i + 1) * td),
						bold: obj.bold
					};
					if (i < obj.line) {
						if (i == obj.line - 1) {
							txt.text = txt.text.substring(0, txt.text.length - 3) + '......';
						}
						this.drawText(ctx, txt);
					}
				}
			},
			/**
			 * 渲染文字
			 *
			 * @param {Object} obj
			 */
			drawText: function(ctx, obj) {
				console.log('渲染文字', obj)
				ctx.save();
				ctx.setFillStyle(obj.color);
				ctx.setFontSize(obj.size);
				ctx.setTextAlign(obj.align);
				ctx.setTextBaseline(obj.baseline);
				if (obj.bold) {
					ctx.fillText(obj.text, obj.x, obj.y - 0.1);
					ctx.fillText(obj.text, obj.x - 0.1, obj.y);
				}
				ctx.fillText(obj.text, obj.x, obj.y);
				if (obj.bold) {
					ctx.fillText(obj.text, obj.x, obj.y + 0.1);
					ctx.fillText(obj.text, obj.x + 0.1, obj.y);
				}
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
						uni.hideLoading();
						if (!res.authSetting['scope.writePhotosAlbum']) {
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
												console.log(err);
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
<style scoped lang="scss">
	.mycanvas {
		margin: 20upx auto 0;
		box-shadow: 0upx 0upx 10upx #ddd;
		border-radius: 10upx;
		overflow: hidden;
	}
	.saveImg{
	  width: 80%;
	  height: 80upx;
	  line-height: 80upx;
	  text-align: center;
	  color: #fff;
	  background-color: #4DB8E4;
	  border-radius: 10upx;
	  margin: 40upx auto 20upx;
	}
	.imgs{
		position: absolute;
		top: 0;
		left: 50%;
		transform: translateX(-50%);
	}
	.tips{
		text-align: center;
		color: #999;
		font-size: 24upx;
		margin-top: 20upx;
	}
</style>
