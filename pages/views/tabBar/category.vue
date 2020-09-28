<template>
	<!--pages/views/tabBar/category.wxml-->
	<view class="category">
		<!-- 搜索 -->
		<view class="search">
			<search class="search_box"></search>
			<view class="hierarchy" @tap="onswitch">
				<!-- 此处切换二级或三级分类 可根据需求选择一种分类方式 -->
				<text class="iconfont icon-erji" v-if="classLevel == true" :style="'font-size: 44upx;color:' + colors"></text>
				<text class="iconfont icon-sanji" v-if="classLevel == false" :style="'font-size: 44upx;color:' + colors"></text>
			</view>
		</view>
		<!-- 二级类型的分类菜单 -->
		/**
		* hideShow 显示销量与价格筛选条件
		*/
		<twostage :colors="colors" :current="currentTwo" :dataList="dataList" :classList="categoryList" v-if="classLevel == true"
		 :hideShow="true" @setTwo="setTwo"></twostage>
		<!-- 三级类型的分类菜单 -->
		<threestage :colors="colors" :current="currentThere" :classList="categoryList" :classData="classData" @setThere="setThere"
		 v-if="classLevel == false"></threestage>
	</view>
</template>

<script>
	var app = getApp();
	import search from "../../commponent/public/search";
	import twostage from "../../commponent/cate/twostage";
	import threestage from "../../commponent/cate/threestage";

	export default {
		data() {
			return {
				classLevel: true, //控制显示分类菜单  true：二级分类菜单   false：三级分类菜单
				colors: "",
				current: "",
				scrollTop: "",
				categoryList: [{ //分类列表
					id: 1,
					name: '饼干',

				}, {
					id: 2,
					name: '布丁',

				}, {
					id: 3,
					name: '甜甜圈',

				}, {
					id: 4,
					name: '面包',

				}, {
					id: 5,
					name: '薯片',

				}, {
					id: 6,
					name: '热狗',

				}, {
					id: 7,
					name: '冰淇淋',

				}, {
					id: 8,
					name: '奶油草莓',

				}, {
					id: 9,
					name: '披萨',

				}, {
					id: 10,
					name: '蛋黄酥',

				}],
				classData: [{ //三级菜单分类
					name: '奥利奥',
					img: "//img30.360buyimg.com/popshop/jfs/t11845/334/820275483/7333/9f90acff/59f8a38eN8d63b2bc.png"
				}, {
					name: '三只松鼠',
					img: "//img20.360buyimg.com/popshop/jfs/t4702/64/3954700922/7363/12853e7c/5907f2ddN0a96cf54.jpg"
				}, {
					name: '良品铺子',
					img: "//img30.360buyimg.com/popshop/jfs/t3472/26/559250389/5485/27928c27/580da7e0Nb2583823.jpg"
				}, {
					name: '达利园',
					img: "//img20.360buyimg.com/popshop/jfs/t2884/157/722435011/5877/a029e06c/57233313Nbb5585a4.jpg"
				}, {
					name: '稻香村',
					img: "//img30.360buyimg.com/popshop/jfs/t4684/82/3914974292/6978/65085974/5907f2c5N4fbef2f6.jpg"
				}, {
					name: '百草味',
					img: "//img20.360buyimg.com/popshop/jfs/t2887/304/855683637/4953/8cf426e2/572966eeN007d6ad3.jpg"
				}, {
					name: '盼盼',
					img: "//img30.360buyimg.com/popshop/jfs/t2632/87/866057601/8941/50435bf/57296832Nbc33fdaf.jpg"
				}, {
					name: '徐福记',
					img: "//img20.360buyimg.com/popshop/jfs/t27217/50/2596273954/4783/c9a93e75/5c05e719Nd0cb6633.jpg"
				}, ],
				// 二级菜单目录
				dataList: [{
						title: 'DUNKINDONUTS唐恩都乐美国甜甜圈6个礼盒装 随机搭配6款',
						type: 1,
						goods_id: 201,
						money: '35.90',
						number: 1,
						hmoney: '45.90',
						img: '/static/images/goods/one.jpg',
						youhui: true,
						baoyou: false,
						status: 1, //商品过期状态  0正常  1已失效
						stock: 600,
						sku: [{
							sku_id: 1,
							skuname: '口味',
							child: [{
									tagname: '醇黑巧克力【20枚】',
									id: 2011,
									imgs: 'http://img10.360buyimg.com/n1/jfs/t1/86401/35/12206/357766/5e43b59cE5a7aa4dd/0753be765166c195.jpg',
									money: '175.78'
								},
								{
									tagname: '草莓味【8枚】',
									id: 2012,
									imgs: 'http://img11.360buyimg.com/n1/jfs/t1/74434/3/6892/331750/5d512febE54e891c4/0096ad20c3c20d23.jpg',
									money: '35.90'
								}
							]
						}],
						skuArr: [{
								goods_sku_arr: ['2011'],
								goods_sku_text: '醇黑巧克力【20枚】',
								img: 'http://img10.360buyimg.com/n1/jfs/t1/86401/35/12206/357766/5e43b59cE5a7aa4dd/0753be765166c195.jpg',
								money: '175.78',
								stock: 345
							},
							{
								goods_sku_arr: ['2012'],
								goods_sku_text: '草莓味【8枚】',
								img: 'http://img11.360buyimg.com/n1/jfs/t1/74434/3/6892/331750/5d512febE54e891c4/0096ad20c3c20d23.jpg',
								money: '35.90',
								stock: 255
							},
						]
					},
					{
						title: '真巧 巧克力涂层甜甜圈 早餐蛋糕手撕面包休闲小零食办公室小吃零嘴下午茶点心 500g甜甜圈（拉花款）',
						type: 2,
						goods_id: 202,
						money: '29.9',
						number: 75,
						hmoney: '39.90',
						img: '/static/images/goods/two.jpg',
						youhui: false,
						baoyou: true,
						status: 0, //商品过期状态  0正常  1已失效
						stock: 100,
						sku: [{
							sku_id: 1,
							skuname: '口味',
							child: [{
									tagname: '500g甜甜圈（彩针款）',
									id: 2021,
									imgs: 'http://img14.360buyimg.com/n1/jfs/t1/72185/1/7121/222065/5d5377d5E46e681fa/6f100c77965b9165.jpg',
									money: '39.90'
								},
								{
									tagname: '500g甜甜圈（拉花款）',
									id: 2022,
									imgs: 'http://img14.360buyimg.com/n1/jfs/t1/72191/2/6973/239664/5d5377d5Ef952d7d9/606e70d2b6dd44d2.jpg',
									money: '39.90'
								}
							]
						}],
						skuArr: [{
								goods_sku_arr: ['2021'],
								goods_sku_text: '500g甜甜圈（彩针款）',
								img: 'http://img14.360buyimg.com/n1/jfs/t1/72185/1/7121/222065/5d5377d5E46e681fa/6f100c77965b9165.jpg',
								money: '39.90',
								stock: 50
							},
							{
								goods_sku_arr: ['2022'],
								goods_sku_text: '500g甜甜圈（拉花款）',
								img: 'http://img14.360buyimg.com/n1/jfs/t1/72191/2/6973/239664/5d5377d5Ef952d7d9/606e70d2b6dd44d2.jpg',
								money: '39.90',
								stock: 50
							},
						]

					},
					{
						title: '钟薛高 钟意你系列 特牛乳*4片 丝绒可可*4片 半巧主义*2 冰淇淋生鲜雪糕 10片装',
						type: 3,
						goods_id: 203,
						money: '152.00 ',
						number: 1,
						hmoney: '162.00',
						img: '/static/images/goods/there.jpg',
						youhui: true,
						baoyou: true,
						status: 0, //商品过期状态  0正常  1已失效
						stock: 200,
						sku: [],
						skuArr: []
					},
					{
						title: '农谣人 原味火山石烤肠1000g/约16根台式原味肠地道肠纯肉肠热狗肠台湾烤肠香肠烧烤肠半熟食火腿肠 台式原味地道肠1kg',
						type: 6,
						goods_id: 204,
						money: '52.00 ',
						number: 1,
						hmoney: '99.00 ',
						youhui: false,
						baoyou: false,
						stock: 100,
						img: '/static/images/goods/six.jpg',
						status: 0, //商品过期状态  0正常  1已失效
						sku: [{
							skuname: '口味',
							sku_id: 1,
							child: [{
									tagname: '台式原味地道肠1kg',
									id: 2041,
									imgs: 'http://img10.360buyimg.com/n1/jfs/t1/118993/11/329/175715/5e8ac0afE94234346/3ceb1344cf34d655.jpg',
									money: '52.00 '
								},
								{
									tagname: '台式黑椒味地道肠1kg',
									id: 2042,
									imgs: 'http://img11.360buyimg.com/n1/jfs/t1/114876/9/17594/220403/5f5ae35bEc7bb735b/bcf0c1017e86894c.png',
									money: '53.50'
								}
							]
						}, ],
						skuArr: [{
								goods_sku_arr: ['2041'],
								goods_sku_text: '台式原味地道肠1kg',
								img: 'http://img10.360buyimg.com/n1/jfs/t1/118993/11/329/175715/5e8ac0afE94234346/3ceb1344cf34d655.jpg',
								money: '52.00',
								stock: 50
							},
							{
								goods_sku_arr: ['2042'],
								goods_sku_text: '台式黑椒味地道肠1kg',
								img: 'http://img11.360buyimg.com/n1/jfs/t1/114876/9/17594/220403/5f5ae35bEc7bb735b/bcf0c1017e86894c.png',
								money: '53.50',
								stock: 50
							},
						]
					},
					{
						title: '巧妈妈 鸡蛋布甸 下午茶休闲零食儿童果冻布丁125g双层果酱味smzdm 4杯鸡蛋布甸（双层）',
						type: 4,
						goods_id: 205,
						money: '25.80',
						number: 1,
						hmoney: 35.00,
						img: '/static/images/goods/four.jpg',
						youhui: true,
						baoyou: false,
						stock: 500,
						status: 0, //商品过期状态  0正常  1已失效
						skuArr: [{
								goods_sku_arr: ['10', '40'],
								goods_sku_text: '鸡蛋布旬 4杯装',
								img: '/static/images/goods/four.jpg',
								money: '25.80',
								stock: 50
							},
							{
								goods_sku_arr: ['10', '50'],
								goods_sku_text: '鸡蛋布旬 6杯装',
								img: '/static/images/goods/four.jpg',
								money: '32.80',
								stock: 10
							},
							{
								goods_sku_arr: ['10', '60'],
								goods_sku_text: '鸡蛋布旬 8杯装',
								img: '/static/images/goods/four.jpg',
								money: '52.80',
								stock: 60
							},
							{
								goods_sku_arr: ['20', '60'],
								goods_sku_text: '乳酸菌布甸（草莓酱 8杯装）',
								img: 'http://img11.360buyimg.com/n1/jfs/t1/97403/35/15115/138620/5e6eeae8E8dbb7c3b/90a57a2a3e696b80.jpg',
								money: '52.80',
								stock: 100
							},
							{
								goods_sku_arr: ['30', '50'],
								goods_sku_text: '乳酸菌布甸（蓝莓酱 8杯装）',
								img: 'http://img12.360buyimg.com/n1/jfs/t1/127005/26/7147/367042/5f0eb18cE9efa12ea/1a8363f7ce5a06cb.jpg',
								money: '32.80',
								stock: 1300
							},
						],
						sku: [{
								sku_id: 1,
								skuname: '口味',
								child: [{
										tagname: '鸡蛋布旬',
										id: 10,
										imgs: '/static/images/goods/four.jpg',
										money: '25.80'
									},
									{
										tagname: '乳酸菌布甸（草莓酱）',
										id: 20,
										imgs: 'http://img11.360buyimg.com/n1/jfs/t1/97403/35/15115/138620/5e6eeae8E8dbb7c3b/90a57a2a3e696b80.jpg',
										money: '14.80'
									},
									{
										tagname: '乳酸菌布甸（蓝莓酱）',
										id: 30,
										imgs: 'http://img12.360buyimg.com/n1/jfs/t1/127005/26/7147/367042/5f0eb18cE9efa12ea/1a8363f7ce5a06cb.jpg',
										money: '30.80'
									}
								]
							},
							{
								sku_id: 2,
								skuname: '数量',
								child: [{
										tagname: '4杯装',
										id: 40,
										imgs: '',
										money: '25.80'
									},
									{
										tagname: '6杯装',
										id: 50,
										imgs: '',
										money: '32.80'
									},
									{
										tagname: '8杯装',
										id: 60,
										imgs: '',
										money: '52.80'
									}
								]
							},
						]
					},
					{
						title: '草莓云南夏季草莓新鲜水果3斤礼盒装 露天种植现摘现发 3斤精品装（4盒顺丰空运）',
						type: 5,
						goods_id: 206,
						money: '59.90',
						number: 200,
						hmoney: '70.90',
						youhui: true,
						baoyou: true,
						img: '/static/images/goods/five.jpg',
						status: 0, //商品过期状态  0正常  1已失效
						stock: 140,
						sku: [{
							sku_id: 1,
							skuname: '种类',
							child: [{
									tagname: '3斤精品装',
									id: 2061,
									imgs: 'http://img10.360buyimg.com/n1/jfs/t1/71401/15/15968/470755/5ddb8ecaEe6a5ce65/140942226e7c7551.jpg',
									money: '59.90'
								},
								{
									tagname: '5斤精品装',
									id: 2062,
									imgs: 'http://img10.360buyimg.com/n1/jfs/t1/82339/35/16255/632261/5ddb8ecdE628ab494/cacc1d2241e9f65f.jpg',
									money: '82.90'
								},
							]
						}, ],
						skuArr: [{
								goods_sku_arr: ['2061'],
								goods_sku_text: '3斤精品装',
								img: 'http://img10.360buyimg.com/n1/jfs/t1/71401/15/15968/470755/5ddb8ecaEe6a5ce65/140942226e7c7551.jpg',
								money: '59.90',
								stock: 80
							},
							{
								goods_sku_arr: ['2062'],
								goods_sku_text: '5斤精品装',
								img: 'http://img10.360buyimg.com/n1/jfs/t1/82339/35/16255/632261/5ddb8ecdE628ab494/cacc1d2241e9f65f.jpg',
								money: '82.90',
								stock: 60
							}
						],
					}
				],
				currentTwo: 0,
				currentThere: 0
			};
		},

		components: {
			search,
			twostage,
			threestage
		},
		props: {},

		/**
		 * 生命周期函数--监听页面加载
		 */
		onLoad: function(options) {},

		/**
		 * 生命周期函数--监听页面初次渲染完成
		 */
		onReady: function() {},

		/**
		 * 生命周期函数--监听页面显示
		 */
		onShow: function() {
			this.setData({
				colors: app.globalData.newColor
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
			setCurrent(e) {
				//设置tab
				let index = e.currentTarget.dataset.index;
				let items = e.currentTarget.dataset.item;
				let scrollTop = index * 20;

				if (index <= 2) {
					scrollTop = 0;
				}

				this.setData({
					current: index,
					scrollTop: scrollTop
				});
			},
			onswitch() {
				this.setData({
					classLevel: !this.classLevel
				});
			},
			setThere(item) { //获取选择的三级tab
				console.log(item)
			},
			setTwo(item) { //获取选择的二级tab
				console.log(item)
			}
		}
	};
</script>
<style scoped lang="scss">
	.category {
		background-color: #ffffff;
		position: fixed;
		width: 100%;
		height: 100%;
		box-sizing: border-box;
		top: 0;
		bottom: 0;
	}

	.search {
		padding: 10upx 4% 10upx;
		padding-left: 10upx;
		background-color: #ffffff;
		overflow: hidden;
		display: flex;
		align-items: center;
		justify-content: space-between;
	}

	.search_box {
		width: 90%;
	}

	.hierarchy {
		width: 10%;
		height: 60upx;
		font-size: 32upx;
		text-align: right;
	}
</style>
