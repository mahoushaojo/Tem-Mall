###已实现页面

* 图片预览

  ![Image text](https://gitee.com/mahoushaojo/tem-mall/raw/master/z_readmeImg/1.jpg)

  ![Image text](https://gitee.com/mahoushaojo/tem-mall/raw/master/z_readmeImg/2.jpg)

  ![Image text](https://gitee.com/mahoushaojo/tem-mall/raw/master/z_readmeImg/3.jpg)

  ![Image text](https://gitee.com/mahoushaojo/tem-mall/raw/master/z_readmeImg/4.png)

  ![Image text](https://gitee.com/mahoushaojo/tem-mall/raw/master/z_readmeImg/5.png)

* tabbar

  * 首页
  * 分类页
  * 购物车页
  * 个人中心页

* 登录

  * 登录页

* 下级页面
  * 设置
  * 商品列表
  * 商品详情
  * 商品评价列表
  * 评价商品
  * 确认订单
  * 支付结果
  * 优惠券列表
  * 我的收藏
  * 收货地址
  * 编辑收货地址
  * 我的积分
  * 兑换礼品
  * 分销中心
  * 我的订单
  * 订单详情
  * 取消订单
  * 退换/售后
  * 申请售后
  * 提现
  * 提现明细
  * 我的下级
  * 推广海报
  * 搜索
  * 选择位置
  * 我的足迹

###模板说明
> * 所有的数据都是静态数据、可直接使用。
> * 目前仅测试支持H5与微信小程序。
> * 登录方式为手机验证码与微信授权登录。
> * 具备换肤功能、后期新增页面需要引入全局变量的(app.globalData.newColor) 来对元素进行绑定。
>   项目模块化 tabBar内的组件都是拆分成模块。
> * 可使用原生小程序的this.setData 对组件内部变量进行修改。
> * 引入了ColorUi样式库。
> * 引入了腾讯地图
> * 引入了和风天气
> * 项目主题文件为 utils>config.js
> * 地址JS文件为 utils>area.js
> * 地址选择器 setcity
> * loading组件 loading.vue
> * 无数据显示组件 nodata.vue
> * auth.js 用于存储全局数据缓存的数据方法
> * request.js 为全局异步请求函数 可在main.js中引入全局进行使用使用方法如下
>
> ```
> request('接口名称',{key:value},'请求方式(默认为GET)')
>  	.then(res=>{console.log(res)})
> ```
>
> * 个人中心页面借鉴RF商城效果、并感谢作者。
> * 期待支持与反馈。