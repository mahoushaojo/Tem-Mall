<template>
	<view class="map">
		<!-- h5项目下 获取个人位置(选择提货点) -->
		<web-view src="https://apis.map.qq.com/tools/locpicker?search=1&type=1&key=7JFBZ-V2EH4-3CCU2-DL5N2-DZQL7-RTBOJ&referer=myapp"></web-view>
	</view>
</template>

<script>
import {setlocation} from '@/utils/auth.js'
// #ifdef H5
window.addEventListener('message', event=> {
  // 接收位置信息，用户选择确认位置点后选点组件会触发该事件，回传用户的位置信息
    var loc = event.data;
	if (loc && loc.module == 'locationPicker') {
        //防止其他应用也会向该页面post信息，需判断module是否为'locationPicker'
		let location = {
				poiaddress: loc.poiaddress,
				poiname: loc.poiname,
				latlng: loc.latlng
			}
        setlocation(location)
		uni.switchTab({
			url:'/pages/views/tabBar/home'
		})
	}
}, false);
// #endif
</script>

<style></style>
