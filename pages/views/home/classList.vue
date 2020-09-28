<template>
<view class="classList">
<!-- #ifdef MP -->
<view class="status_bar" :style="'height: '+ statusBarHeight"></view>
<!-- #endif -->
 <view class="top_nav">
 	<!-- 搜索框 -->
 	<view class="search_box">
 	  <view class="backTo" @click="backTo">
 			<text class="iconfont icon-fanhui"></text>
 	  </view>
 	 <view class="search">
 	   <view class="input-box" >
 	 		<input placeholder="搜索关键词" placeholder-style="color:#c0c0c0;font-size:24upx;"></input>
 	 		<view class="icon search">
 	       <image src="/static/images/home/search.png"></image>
 	     </view>
 	 	</view>
 	 </view>
 	 <view class="bg"></view>
 	</view>
 	<!-- 条件筛选框 -->
 	<view class="screen">
 	  <view class="screen_list" @tap="openScreen" :style="'color:' + (current == 0 ? colors:'')" data-index="0">
 	    {{screenName}} 
 	    <text class="iconfont icon-xiasanjiao down" :style="'color:' + (current == 0 ? colors:'')"></text>
 	  </view>
 	  <view class="screen_list" @tap="setCurrent" :style="'color:' + (current == 1 ? colors:'')" data-index="1">
 	    销量
 	  </view>
 	  <view class="screen_list" @tap="setList">
 	    <text class="iconfont icon-gongge" :style="'font-size:30upx;font-weight:bold;color:' + (current == 2 ? colors:'#333')" v-if="modes == true"></text>
 	    <text class="iconfont icon-liebiao" :style="'font-size:30upx;font-weight:bold;color:' + (current == 2 ? colors:'#333')" v-if="modes == false"></text>
 	  </view>
 	  <view class="screen_list" @tap="openPop" :style="'color:' + (current == 3 ? colors:'')" data-index="3">
 	    筛选
 	  </view>
 	  <!-- 弹出筛选框 -->
 	  <view class="more_screen" :style="'opacity:' + (isMaskShow == true?'1':'0') + ';display:' + (isMaskShow == true?'block':'none')">
 	     <view v-for="(item, index) in screenList" :key="index" class="more_list" @tap="setScreen(item,index)">
 	       <text :style="'font-weight:' + (onscreen == index ? 'bold':'500')">{{item.name}}</text>
 	       <text v-if="onscreen == index" class="iconfont icon-duihao" :style="'color:' + colors + ';'"></text>
 	     </view>
 	  </view>
 	</view>
 	<!-- 弹出遮罩层 -->
 	<view class="mask onemask" @tap="closeScreen" catchtouchmove="true" :style="'opacity:' + (isMaskShow == true?'1':'0') + ';z-index:' + (isMaskShow == true?'800':'-11')+';top:'+statusBarHeight+';'"></view>
 	<view class="mask" @tap="hideright" catchtouchmove="true" :style="'opacity:' + (popShow == true?'1':'0') + ';z-index:' + (popShow == true?'910':'-11')"></view>
 	<!-- 点击筛选弹出的右侧弹出窗口 -->
 	<view :class="popShow == true ? 'right_popup':'hide_popup'" catchtouchmove="true">
 	  <scroll-view class="pop_scroll">
 	    <view class="top_price">
 	      <text>价 格 区 间</text>
 	      <text :style="'color:' + colors">请输入价格区间</text>
 	    </view>
 	    <view class="input_box">
 	      <input placeholder="最低价"></input>
 	      <text>-</text>
 	      <input placeholder="最高价"></input>
 	    </view>
 	    <view v-for="(item, index) in classList" :key="index" class="allClass">
 	      <view class="classtext">{{item.name}}</view>
 	      <view class="class_box">
 	        <!-- 选中分类条件 -->
 	        <view v-for="(row, indexs) in item.child" :key="indexs" class="class_tag" :style="'color:' + (row.current == true ? '#fff':'#333') + ';background:' + (row.current == true ? colors:'') + ';border-color:' + (row.current == true ? colors:'') + ';'" @tap="setClass(item,row,index,indexs)">{{row.name}}</view>
 	      </view>
 	    </view>
 	  </scroll-view>
 	  <view class="bottom_btn">
 	    <view class="reset" :style="'color:' + colors + ';border-color:' + colors" @tap="onreset">重置</view>
 	    <view class="enter" :style="'background:' + colors" @tap="onenter">确定</view>
 	  </view>
 	</view>
	<!-- 二级条件选择 -->
	<scroll-view scroll-x="true" class="tow_scroll" :scroll-with-animation="true" :scroll-left="scrollLeft">
		<view class="scroll_tags" :style="{color:tagCurrent==index?'#ffffff':'#202020',background:tagCurrent==index?colors:'#F5F5F5'}" v-for="(item,index) in tagsList" :key="index" @click="setTags(item,index)">
			{{item}}
		</view>
	</scroll-view>
 </view>
  <!-- 商品列表 -->
  <scroll-view scroll-y="true" class="scroll_box" :style="{paddingBottom: statusBarHeight}">
  	<recommend :colors="colors" :dataList="dataList" :modes="modes" :loading="loading"></recommend>
  </scroll-view>
  <loading v-if="isShow == true"></loading>
</view>
</template>

<script>
var app = getApp();
import search from "../../commponent/public/search";
import recommend from "../../commponent/home/recommend";
import loading from "../../commponent/public/loading";

export default {
  data() {
    return {
	  statusBarHeight: app.globalData.statusHeight + 'px',
	  toBarHeight: app.globalData.toBar + 'px',
      colors: '',
      classId: '',
      isShow: true,
      current: 0,
      isMaskShow: false,
      modes: true,
      onscreen: 0,
	  scrollLeft: 0,
	  tagCurrent: 999,
	  loading: false,
      screenList: [{
        name: '综合',
        id: 0
      }, {
        name: '价格升序',
        id: 1
      }, {
        name: '价格降序',
        id: 2
      }],
      screenName: '综合',
      popShow: false,
      classList: [{
        name: '全部分类',
        id: 1,
        child: [{
          name: '资源类',
          id: 2
        }, {
          name: '工具类',
          id: 3
        }]
      }, {
        name: '品牌',
        id: 2,
        child: [{
          name: '苹果',
          id: 1
        }, {
          name: '华为',
          id: 2
        }, {
          name: '小米',
          id: 2
        }]
      }],
      newdata: "",
	  tagsList:[
		  '苹果','小米','华为','诺基亚','联想','vivo','oppo','魅族','中兴','美颜','锤子'
	  ],
	  // 商品列表
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
    };
  },

  components: {
    search,
    recommend,
    loading
  },
  props: {},

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
    let classId = options.classId;
    this.setData({
      colors: app.globalData.newColor,
      classId: classId
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
  onReady: function () {},

  /**
   * 生命周期函数--监听页面显示
   */
  onShow: function () {},

  /**
   * 生命周期函数--监听页面隐藏
   */
  onHide: function () {},

  /**
   * 生命周期函数--监听页面卸载
   */
  onUnload: function () {},

  /**
   * 页面相关事件处理函数--监听用户下拉动作
   */
  onPullDownRefresh: function () {},

  /**
   * 页面上拉触底事件的处理函数
   */
  onReachBottom: function () {},

  /**
   * 用户点击右上角分享
   */
  onShareAppMessage: function () {},
  methods: {
    setCurrent(e) {
      console.log(e);
      let index = e.currentTarget.dataset.index;
      this.setData({
        current: index
      });
    },

    openScreen(e) {
      let index = e.currentTarget.dataset.index;
	  console.log(e)
      this.setData({
        current: index,
        isMaskShow: !this.isMaskShow
      });
    },

    closeScreen() {
      this.setData({
        isMaskShow: false
      });
    },

    setList() {
      this.setData({
        modes: !this.modes
      });
    },

    setScreen(item,index) {
      this.setData({
        onscreen: index,
        screenName: item.name,
        isMaskShow: false
      });
    },

    openPop() {
      this.setData({
        popShow: true
      });
    },

    hideright() {
      this.setData({
        popShow: false
      });
    },

    setClass(item,row,index,indexs) { //选择条件
	  row.current = row.current == true ? false : true
      item.child[indexs] = row;
      let data = item;
      let newdata = 'classList[' + index + ']';
      this.setData({
        [newdata]: data
      });
    },

    onreset() { //重置筛选条件
      let data = this.classList;
      data.forEach(e => {
        e.child.forEach(ele => {
          ele.current = false;
        });
      });
      this.setData({
        classList: data,
		popShow: false
      });
    },

    onenter() { //确认筛选条件
      this.setData({
        popShow: false
      });
	  let select = []
	  this.classList.forEach(e=>{
		  e.child.forEach(ele => {
		     if(ele.current == true){
				 select.push(ele)
			 }
		  });
	  })
	  console.log('选中的项', select)
    },
	backTo(){
		uni.navigateBack(-1)
	},
	setTags(item, index){ //选择tag二级分类
		this.tagCurrent = index
		this.isShow = true
		let scrollLeft = index * 38;
		if (index <= 2) {
		  scrollLeft = 0;
		}
		this.scrollLeft = scrollLeft
		setTimeout(()=>{
			this.isShow = false
		},500)
	}
  }
};
</script>
<style scoped lang="scss">
.classList{
	width: 100%;
	height: 100%;
	position: fixed;
	top: 0;
	bottom: 0;
}
.top_nav{
	height: 240upx;
	background-color: #FFFFFF;
	border-bottom: 1upx solid rgba($color: #eee, $alpha: 0.6);
}
.status_bar{
  width: 100vw;
  overflow: hidden;
  display: block;
  background-color: #FFFFFF;
  z-index: 900;
}
.scroll_box{
	height: calc(100vh - 260upx);
}
.search_box{
  padding: 0 20upx;
  z-index:900; 
  height: 80upx;
  width: 100%;
  padding-top: 30upx;
  position: relative;
  display: flex;
  align-items: center;
  .backTo{
	  color: #202020;
	  z-index: 20;
	  margin-right: 30upx;
	  width: 8%;
	  text-align: center;
	  text{
		   font-size: 34upx;
	  }
  }
  .search{
	  /* #ifdef H5 */
	  width: 90%;
	  /* #endif */
	  /* #ifdef MP */
	  width: 60%;
	  /* #endif */
	  z-index: 900;
	  .input-box {
	    width: 100%;
	    height: 60upx;
	    background-color: #f5f5f5;
	    position: relative;
	    display: flex;
	    align-items: center;
	    border-radius: 30upx;
	    z-index: 900;
	  }
	  
	  .input-box .icon {
	    display: flex;
	    align-items: center;
	    position: absolute;
	    top: 10upx;
	    right: 20upx;
	    width: 40upx;
	    height: 40upx;
	    font-size: 32upx;
	    color: #c0c0c0;
	  }
	  .search image{
	    width: 40upx;
	    height: 40upx;
	    display: block;
	  }
	  .input-box input {
	    padding-left: 28upx;
	    height: 28upx;
	    font-size: 28upx;
	  }
  }
}
.search_box .bg{
  width: 100%;
  height: 100%;
  background-color: #fff;
  position: absolute;
  top: 0;
  left: 0;
}
.screen{
  height: 80upx;
  line-height: 80upx;
  display: flex;
  align-items: center;
  padding: 0 10upx;
  z-index: 900;
  position: relative;
  background-color: #fff;
}
.screen .screen_list {
  flex: 1;
  text-align: center;
  font-size: 28upx;
  color: #333;
  font-weight: 500;
  .down{
	margin-left: 10upx;  
  }
}
.screen_list text{
  font-size: 24upx;
  font-weight: normal;
}
.more_screen{
  width: 100%;
  padding: 0 60upx;
  border-bottom-left-radius: 20upx;
  border-bottom-right-radius: 20upx;
  position: absolute;
  background-color: #fff;
  top: 80upx;
  left: 0;
  padding-bottom: 20upx;
  opacity: 0;
  transition: all 0.3s;
}
.more_list{
  height: 80upx;
  line-height: 80upx;
}
.more_list .iconfont{
  float: right;
}
.mask{
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.8);
  z-index: 800;
  opacity: 0;
  /* transition: all 0.3s; */
}
/* 右侧弹出框 */
.right_popup{
  width: 85%;
  height: 100%;
  position: fixed;
  right: 0;
  top: 0;
  background-color: #fff;
  z-index: 920;
  transition: all 0.3s;
}
.hide_popup{
  transition: all 0.3s;
  width: 85%;
  height: 100%;
  position: fixed;
  right: -100%;
  top: 0;
  z-index: 920;
  transition: all 0.3s;
  background-color: #fff;
}
.pop_scroll{
  /* #ifdef MP */
  height: calc(90% - 120upx);
  margin-top: 120upx;
  /* #endif */
  /* #ifdef H5 */
  height: 90%;
  margin-top: 20upx;
  /* #endif */
}
.top_price{
  height: 80upx;
  line-height: 80upx;
  display: flex;
  padding: 0 20upx;
}
.top_price text{
  flex: 1;
  text-align: center;
  font-size: 28upx;
  font-weight: bold;
  color: #333;
}
.input_box{
  padding: 0 30upx;
  height: 60upx;
  line-height: 60upx;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-top: 30upx;
}
.input_box text{
  color: #999;
}
.input_box input{
  flex: 1;
  max-width: 45%;
  height: 100%;
  background-color: #F7F7F7;
  border-radius: 40upx;
  box-sizing: border-box;
  text-align: center;
  font-size: 24upx;
}
.allClass{
  margin-top: 50upx;
  padding: 0 30upx;
}
.allClass .classtext{
  font-size: 28upx;
  font-weight: bold;
  color: #333;
}
.allClass .class_box{
  margin-top: 30upx;
  overflow: hidden;
}
.class_box .class_tag{
  height: 60upx;
  line-height: 60upx;
  padding: 0 20upx;
  font-size: 26upx;
  color: #333;
  border: 1upx solid #999;
  text-align: center;
  border-radius: 30upx;
  float: left;
  margin-right: 20upx;
}
.bottom_btn{
  height: 10%;
  display: flex;
  align-items: center;
  align-items: center;
  justify-content: space-between;
  padding: 0 30upx;
}
.bottom_btn view{
  max-width: 45%;
  min-width: 45%;
  height: 60upx;
  line-height: 60upx;
  background-color: #FA436A;
  text-align: center;
  border-radius: 35upx;
  color: #fff;
}
.bottom_btn .reset{
  background-color: #fff;
  color: #FA436A;
  border: 1upx solid #FA436A;
}
 .tow_scroll{
	  height: 80upx;
	  line-height: 80upx;
	  padding: 0 10upx;
	  white-space: nowrap;
	  .scroll_tags{
		  display: inline-block;
		  background-color: #FFF8F5;
		  color: #202020;
		  height: 50upx;
		  line-height: 50upx;
		  padding: 0 25upx;
		  text-align: center;
		  font-size: 24upx;
		  border-radius: 30upx;
		  margin: 0 10upx;
	  }
  }
</style>