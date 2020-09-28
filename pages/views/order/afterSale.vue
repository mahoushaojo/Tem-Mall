<template>
<view class="cencal_order">
	<view class="goods_data">
		<image :src="goodData.img" mode=""></image>
		<view class="right">
			<p class="goods_name">{{goodData.title}}</p>
			<p class="goods_sku">{{goodData.goods_sku_text}}</p>
			<p class="goods_price" >
				<text style="font-size: 24upx;">数量x{{goodData.number}}</text>
				<text :style="{color:colors}">￥{{goodData.money}}</text>
			</p>
		</view>
	</view>
  <view class="remark">
    <textarea maxlength="-1" placeholder="请在此处输入您的退单理由" placeholder-class="textarea_p"></textarea>
  </view>
  <view class="tag_box">
    <view v-for="(item, index) in remarkList" :key="index" class="tag_list" @tap="setCurrent(index)">
      <view :style="'color:' + (item.current == true ?'#fff':'') + ';background:' + (item.current == true ? colors:'') + ';border:' + (item.current == true ? 'none':'')">
        {{item.name}}
      </view>
    </view>
  </view>
  <p class="youhui" style="border-bottom: none;">
  	<text class="text1">上传凭证</text>
  	<text class="text3">(最多3张)</text>
  </p>
  <view class="img_box" >
  	<view class="img_list" v-for="(item, index) in imgUrl" :key="index">
  		<image :src="item" mode="" class="imgs"></image>
  		<image src="/static/images/close1.png" mode="" class="close" @tap="delImg(index)"></image>
  	</view>
  	<view class="addImg" @tap="addImg" v-if="imgUrl.length < 3">
  		<image src="/static/images/shexiang.png" mode=""></image>
  		<p>添加图片</p>
  	</view>
  </view>
  <view class="btns" :style="{background: colors}">
  	确认提交
  </view>
</view>
</template>

<script>
var app = getApp();
export default {
  data() {
    return {
      colors: '',
      remarkList: [{
        name: '图案不好看'
      }, {
        name: '性价比太低'
      }, {
        name: '态度不好'
      }, {
        name: '价格不合理'
      }, {
        name: '做工不行'
      }, {
        name: '物流时间长'
      }, {
        name: '价格优惠低'
      }, {
        name: '其他原因'
      }],
      data: "",
	  imgUrl:[],
	  goodData:{}
    };
  },

  components: {},
  props: {},

  /**
   * 生命周期函数--监听页面加载
   */
  onLoad: function (options) {
	let goodData = JSON.parse(options.goodData)
    this.setData({
      colors: app.globalData.newColor,
	  goodData:goodData
    });
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
    setCurrent(index) {
      let remark = this.remarkList[index];
	  remark.current = !remark.current
      let data = 'remarkList[' + index + ']';
      this.setData({
        [data]: remark
      });
      let arr = [];
      this.remarkList.forEach(e => {
        if (e.current == true) {
          arr.push(e);
        }
      });
      console.log(arr);
    }
  }
};
</script>
<style lang="scss" scoped>
page{
  background-color: #F5F5FA;
}
.goods_data{
		display: flex;
		align-items: center;
		justify-content: flex-start;
		overflow: hidden;
		image{
			width: 140upx;
			height: 140upx;
			border-radius: 8upx;
		}
		.right{
			margin-left: 20upx;
			width: 80%;
			.goods_name{
				font-size: 26upx;
				overflow: hidden;
				display: -webkit-box;
				-webkit-line-clamp:2;
				-webkit-box-orient: vertical;
			}
			.goods_sku{
				font-size: 24upx;
				color: #999999;
				margin-top: 5upx;
			}
			.goods_price{
				display: flex;
				align-items: center;
				justify-content: space-between;
				font-size: 28upx;
				text-align: right;
				color: #999;
				margin-top: 5upx;
			}
		}
	}
.cencal_order{
  padding: 20upx 4%;
  background-color: #fff;
}
.youhui {
	height: 40upx;
	line-height: 40upx;
	padding: 20upx 0;
	border-bottom: 1upx solid #f2f2f2;
	box-sizing: content-box;
	.text1 {
		font-size: 28upx;
		color: #333333;
	}
	.text2 {
		float: right;
		color: #999999;
		font-size: 28upx;
		margin-right: 5upx;
		margin-top: 2upx;
	}
	.text3 {
		font-size: 24upx;
		color: #999999;
		margin-left: 10upx;
	}
	image {
		float: right;
		width: 12upx;
		height: 22upx;
		margin-top: 12upx;
		margin-left: 10upx;
	}
}
.img_box {
	overflow: hidden;
	padding: 20upx 0;
	.addImg {
		width: 184upx;
		height: 184upx;
		background: #f2f2f2;
		// background-color: pink;
		border-radius: 20upx;
		overflow: hidden;
		transition: all 0.3s;
		image {
			width: 51upx;
			height: 42upx;
			display: block;
			margin: 0 auto;
			margin-top: 45upx;
		}
		p {
			font-size: 24upx;
			font-family: Microsoft YaHei;
			font-weight: 400;
			color: rgba(255,94,102,1);
			text-align: center;
			margin-top: 20upx;
		}

		&:active {
			transform: scaleX(0.96);
		}
	}
}
.img_list {
	width: 184upx;
	height: 184upx;
	border-radius: 20upx;
	float: left;
	margin-right: 20upx;
	position: relative;
	.imgs {
		width: 100%;
		height: 100%;
		display: block;
		border-radius: 20upx;
	}
	.close {
		width: 40upx;
		height: 40upx;
		position: absolute;
		top: 0upx;
		right: 0upx;
	}
}
.remark{
  background-color: #F5F5F5;
  border-radius: 10upx;
  height: 200upx;
  padding: 20upx;
  margin-top: 20upx;
  textarea{
	  font-size: 24upx;
	  color: #797979;
	  height: 100%;
  }
}
.textarea_p{
  font-size: 24upx;
  color: #797979;
}
.tag_box{
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  margin-top: 40upx;
}
.tag_box::after{
  content: '';
  width: 30%;
}
.tag_list{
  max-width: 24%;
  min-width: 24%;
  text-align: center;
  margin-bottom: 30upx;
}
.tag_list view{
  height: 60upx;
  line-height: 60upx;
  border-radius: 30upx;
  border: 1upx solid #ddd;
  font-size: 22upx;
  color: #303030;
}
.btns{
	width: 100%;
	height: 80upx;
	line-height: 80upx;
	font-size: 30upx;
	color: #FFFFFF;
	text-align: center;
	margin-top: 100upx;
	border-radius: 8upx;
}
</style>