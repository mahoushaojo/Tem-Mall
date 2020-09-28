<template>
   <!-- 弹出规格选项 -->
    <view class="popup">
      <view class="mask" @touchmove.stop.prevent="moveHandle" v-if="showModal" @tap="onhide"></view>
      <view @touchmove.stop.prevent="moveHandle" :class="'sku ' + (showModal==true ? 'shows':'')" :style="{bottom: showModal == true ? bottoms+'upx': ''}">
        <view class="sku_top">
          <image :src="currentSku.img" class="top_img"></image>
          <view class="sku_title">
           {{nowList.title}}
          </view>
          <view class="moneys">
          <!-- 这里的价格是选择完规格后计算的价格 -->
            ￥{{(Number(currentSku.money) * number).toFixed(2)}}
          </view>
           <view class="kucun">
          <!-- 这里的价格是选择完规格后计算的库存 -->
            库存: {{currentSku.stock}}
          </view>
        </view>
        <block v-if="nowList.sku && nowList.sku.length !== 0">
          <view v-for="(item, index) in nowList.sku" :key="index" class="sku_list">
            <view class="sku_name">
              {{item.skuname}}
            </view>
            <view class="sku_tag">
					<button v-for="(row, s) in item.child" :key="row.id" class="tag_s" :class="{'ondisabled': row.disabled == true}" :disabled="row.disabled == true" @tap="setTag(item,index,s,row)" :style="{color:currentArr[item.sku_id] == row.id ? colors :'#333333',background:currentArr[item.sku_id] == row.id ? '#fff' :'',borderColor:currentArr[item.sku_id] == row.id ? colors :''}">
					     {{row.tagname}}
					</button>
            </view>
          </view>
        </block>
          <view class="number">
            <view class="n_left">购买数量</view>
            <view class="n_right">
              <text class="jian" @tap="onreduce">－</text>
              <input :value="number" disabled type="number" maxlength="2" class="inputs"></input>
              <text class="jia" @tap="onadd">＋</text>
            </view>
          </view>
          <view class="btn_box">
            <view class="addcart_btn" :style="'background-color:' + colors" @tap="onsubmit('add')">加入购物车</view>
            <view class="submit" :style="'background-color:' + colors" @tap="onsubmit('pay')">立即购买</view>
          </view>
      </view>
    </view>
</template>

<script>
import {getCart, setCart , setGoodsData, getToken} from '@/utils/auth.js'
export default {
  data() {
    return {
      number: 1,
      current: "",
	  currentArr:[], //当前选中的规格数组
	  currentSku:{}, //选择后的规格详情
	  skulength: 0, //选择商品规格的长度
	  issku: false ,//判断当前商品是否存在规格
	  update: true,
	  nowList:{}
    };
  },
  components: {},
  props: {
    colors: {
      type: String
    },
    showModal: {
      type: Boolean,
      default: false
    },
    skuList: {
      type: Object
    },
	bottoms:{
	  type: String,
	  default: 0
	}
  },
  computed:{
	  skuArr() {
	  	return this.skuList.skuArr;
	  }
  },
  watch:{
	skuList(value){//监听商品规格变化 来清空之前所选的规格
		this.currentArr = []
		this.issku = false //初始化当前规格的条件
		this.number = 1 //初始数量
		this.skulength = 0 //初始化商品规格长度
		this.currentSku = value
		this.nowList = value
	}  
  },
  methods: {
   moveHandle(){
	 return  
   },
   setTag(items, current, indexs,row) {
     //选择规格
	let that = this
	let item = items
	let pid = items.sku_id
	let isChecked = true; // 选中 or 取消选中
	if (that.currentArr[pid] != undefined && that.currentArr[pid] == row.id) {
		// 点击已被选中的，删除并填充 ''
		isChecked = false;
		that.currentArr.splice(pid, 1, '');
	} else {
		// 选中
		that.$set(that.currentArr, pid, row.id);
	}
	let chooseSkuId = []; // 选中的规格大类
	that.currentArr.forEach(sku => {
		if (sku != '') {
			// sku 为空是反选 填充的
			chooseSkuId.push(sku);
		}
	});
	
	let newSku = that.getAllSku() //获取符合条件的规格数据
	that.skulength = chooseSkuId.length
	if(chooseSkuId.length == that.nowList.sku.length && newSku.length){
		//如果所有的规格类都被选中了 设置当前选中项商品的信息
		that.currentSku = newSku[0]
		that.issku = true //设定当前商品为规格商品 用于加入购物车时判断
	}else{
		that.currentSku = that.nowList;
	}
	// 每次点击选择或者取消之后都要刷新下选择状态 判断其他规格不符合条件的是否被选中
	that.changeDisabled(isChecked,row.id, pid)
   },
   changeDisabled(isChecked=false,skuId=0, pid=0){ //改变禁用状态
	  let newSku = []
	  if (isChecked) { 
	   for(let key of this.skuArr){ //遍历可用规格数组
		   if(key.stock <= 0){ //如果规格现有的库存小于等于0
			   continue
		   }
		   if(key.goods_sku_arr.indexOf(skuId.toString()) >= 0){ //如果当前选中的类中存在对应的规格
			   newSku.push(key)
		   }
	   }
	   }else{
		   newSku = this.getAllSku()
	   }
	   // 所有存在并且有库存未选择的规格项 的 子项 id
	   let noChooseSkuIds = [];
	   for (let price of newSku) {
	   	noChooseSkuIds = noChooseSkuIds.concat(price.goods_sku_arr);
	   }
	   // 去重
	   noChooseSkuIds = Array.from(new Set(noChooseSkuIds));

	   if (isChecked) {
	   	// 去除当前选中的规格项
	   	let index = noChooseSkuIds.indexOf(skuId.toString());
	   	noChooseSkuIds.splice(index, 1);
	   } else {
	   	// 循环去除当前已选择的规格项
	   	this.currentArr.forEach(sku => {
	   		if (sku.toString() != '') {
	   			// sku 为空是反选 填充的
	   			let index = noChooseSkuIds.indexOf(sku.toString());
	   			if (index >= 0) {
	   				// sku 存在于 noChooseSkuIds
	   				noChooseSkuIds.splice(index, 1);
	   			}
	   		}
	   	});
	   }
	   
	   // 当前已选择的规格大类
	   let chooseSkuKey = [];
	   if (!isChecked) {
	   	// 当前已选择的规格大类
	   	this.currentArr.forEach((sku, key) => {
	   		if (sku != '') {
	   			// sku 为空是反选 填充的
	   			chooseSkuKey.push(key);
	   		}
	   	});
	   } else {
	   	// 当前点击选择的规格大类
	   	chooseSkuKey = [pid];
	   }
	   let skuid = this.currentArr[pid]
	   for(let i in this.nowList.sku){
		   // 当前点击的规格，或者取消选择时候 已选中的规格 不进行处理
		   for (var x in this.nowList.sku[i]['child']) {
		   	// 如果当前规格项 id 不存在于有库存的规格项中，则禁用
		   	if (chooseSkuKey.indexOf(this.nowList.sku[i]['sku_id']) >= 0) {
		   		if(this.nowList.sku[i]['child'][x]['id'] == skuid){
		   			continue
		   		}else{
		   			if(!isChecked){
		   				this.nowList.sku[i]['child'][x]['disabled'] = false;
		   			}
		   		}
		   	}else{
		   		if (noChooseSkuIds.indexOf(this.nowList.sku[i]['child'][x]['id'].toString()) >= 0) {
		   			this.nowList.sku[i]['child'][x]['disabled'] = false;
		   		} else {
					this.nowList.sku[i]['child'][x]['disabled'] = true;
		   		}
		   	}
		   }
	   }
   },
    onhide() {
      //隐藏规格对话框
	  this.$emit('onhide')
    },

    onadd() {
      //加
      let data = this.number + 1;
      this.setData({
        number: data
      });
    },

    onreduce() {
      //减
      if (this.number <= 1) {
        return;
      } else {
        let data = this.number - 1;
        this.setData({
          number: data
        });
      }
    },

    onsubmit(value) {
	  // 此处应该判断是否登录 如果没登录 跳转到登录页
	  if(!getToken()){
		uni.navigateTo({ //登录
			url:'/pages/login/login'
		})
		return
	  }
      //提交购物车
     if(this.skulength != this.nowList.sku.length){ //如果规格长度和所选规格长度不相等 提示
     	uni.showToast({
     		title: '请选择规格',
     		icon: 'none'
        });
     }else {
    	//此处应调用接口来添加购物车 现模拟添加到购物车
    	var datas = this.nowList
    	if(this.issku == true){ //判断当前商品是不是规格商品
    		datas.selectSku = this.currentSku
    	}
		datas.number = this.number
		if(value == 'add'){ //如果是添加购物车
			setCart(datas)
			// 存储商品数据
			uni.showToast({
			  title: '加入购物车成功 !',
			  icon: 'none'
			});
			/**
			 * 模拟获取购物车的数量 getCart
			 */
			let cart = getCart().length
			uni.setTabBarBadge({
			  //给tabBar添加角标
			  index: 2,
			  text: String(cart)
			});
		}else{ //如果是购买商品
			let goods = []
			goods.push(datas)
			setGoodsData(goods) //存储商品信息和商品规格
			uni.navigateTo({  //提交订单
				url:'/pages/views/order/confirmOrder'
			})
		}
        setTimeout(() => {
		  this.$emit('onhide')
        }, 800);
      }
    },
	// 处理规格多选情况下 符合条件的规格 并把不符合条件的规格禁用
	getAllSku(){
		let newSku = []
		for(let key of this.skuArr){ //遍历可用规格数组
			if(key.stock <= 0){ //如果规格现有的库存小于等于0
				continue
			}
			var isOk = true
			this.currentArr.forEach((sku)=>{
				// sku 不为空，并且，这个 条 skuPrice 没有被选中,则排除
				if(sku.toString() !== '' && key.goods_sku_arr.indexOf(sku.toString()) < 0){
					isOk = false
				}
			})
			if(isOk == true){
				newSku.push(key)
			}
		}
		return newSku
	}
  }
};
</script>
<style scoped lang="scss">
.mask{
  width: 100%;
  height: 100%;
  position: fixed;
  top: 0;
  left: 0;
  background: #000;
  z-index: 900;
  opacity: 0.7;
}
.sku{
  width: 100vw;
  min-height: 30vh;
  position: fixed;
  bottom: -100%;
  z-index: 910;
  left: 0;
  background-color: #ffffff;
  padding: 20upx 4%;
  border-top-left-radius: 10upx;
  border-top-right-radius: 10upx;
  border-bottom: 1upx solid #eee;
  transition: all 0.3s;
}
.shows{
  bottom: 0;
  transition: all 0.3s;
}
.sku_top{
  overflow: hidden;
  margin-top: 20upx;
}
.sku_top .top_img{
  height: 170upx;
  width: 170upx;
  float: left;
  margin-right: 15upx;
  border-radius: 8upx;
}
.sku_top .sku_title{
  font-size: 30upx;
  line-height: 35upx;
  font-weight: bold;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
}
.sku_top .moneys{
  font-size: 30upx;
  line-height: 40upx;
  overflow: hidden;
  margin-top: 20upx;
  font-weight: bold;
  color: $mycolor;
}
.sku_top .kucun{
  font-size: 24upx;
  color: #999;
  overflow: hidden;
}
.sku_list{
  margin-top: 20upx;
  overflow: hidden;
  margin-bottom: 40upx;
}
.sku_name{
  font-size: 24upx;
  color: #666;
  overflow: hidden;
}
.sku_tag{
  overflow: hidden;
  margin-top: 20upx;
}
.sku_tag .tag_s{
  height: 60upx;
  line-height: 60upx;
  align-items: center;
  padding: 0 20upx;
  text-align: center;
  font-size: 26upx;
  color: #202020;
  background-color: #F5F5F5;
  border: 1upx solid #F5F5F5;
  float: left;
  border-radius: 40upx;
  margin-right: 20upx;
  transition: all 0.2s;
  margin-bottom: 20upx;
  font-weight: 500;
  &::after{
	  border: none;
  }
}
.number{
  margin-top: 10upx;
  border-top: 1upx solid #ccc;
  width: 100%;
  height: 80upx;
  line-height: 80upx;
  padding-top: 10upx;
}
.number .n_left{
  float: left;
  font-size: 28upx;
  color: #333;
}
.number .n_right{
  float: right;
  height: 60upx;
  width: 200upx;
  background-color: #F5F5F5;
  margin-top: 10upx;
  border-radius: 5upx;
}
.n_right .jian,.jia{
  width: 60upx;
  height: 60upx;
  text-align: center;
  line-height: 60upx;
  font-size: 42upx;
}
.jian{
  float: left;
}
.jia{
  float: right;
}
.jian:active{
  background-color: #eee;
}
.jia:active{
  background-color: #eee;
}
.n_right .inputs{
  width: 76upx;
  float: left;
  text-align: center;
  margin-top: 6upx;
}
.btn_box{
  margin-top: 40upx;
}
.btn_box .addcart_btn, .submit{
  width: 40vw;
  height: 60upx;
  line-height: 60upx;
  border-radius: 42upx;
  font-size: 26upx;
  text-align: center;
  color: #ffffff;
  float: left;
  margin-left: 30upx;
  margin-bottom: 30upx;
}
.btn_box .addcart_btn:active{
  opacity: 0.8;
}
.btn_box .submit:active{
  opacity: 0.8;
}
.ondisabled{
	background-color: #F9F9F9;
	opacity: 0.5;
}
</style>