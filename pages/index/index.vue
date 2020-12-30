<template>
	<view class="content">
		<button type="primary" @click="tosign">去签名</button>
		<view style="margin: 10px auto;">
			<image v-if="img" class="img" :src="img" mode=""></image>
			
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				img:''
			}
		},
		onLoad() {

		},
		methods: {
			tosign(){
				uni.navigateTo({
					url:'../singature/singature'
				})
			}
		}
	}
</script>

<style>
	.img{
		transform: rotate(-90deg);
		width: 252rpx;
		/* margin-top: 252rpx; */
		margin-left: 200rpx;
	}
</style>
