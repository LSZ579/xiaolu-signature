<template>
	<view>
		<xiaolu-signature @getImg="getImg"></xiaolu-signature>
	</view>
</template>

<script>
	import XiaoluSignature from '../../components/xiaolu-signature/index.vue'
	export default {
		components:{XiaoluSignature},
		data() {
			return {
				
			}
		},
		methods: {
			getImg(e){
				var pages = getCurrentPages(); 
				var prevPage = pages[pages.length - 2]; 
				//prevPage.img = e//小程序写法
				prevPage.$vm.img =e//h5写法
			}
		}
	}
</script>

<style>

</style>
