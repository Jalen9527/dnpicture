<template>
	<view @click="handleClick">
		<slot></slot>
	</view>
</template>

<script>
	export default {
		props:{
			//接收父组件传递过来的数据
			list:Array,
			index:Number
		},
		//组建挂载完毕之后操作
		mounted() {
			
		},
		methods:{
			handleClick(){
				//1 将数据缓存下来
				getApp().globalData.imgList = this.list;
				getApp().globalData.imgIndex = this.index; 
				//2 实现点击事件
				uni.navigateTo({
					url:"/pages/imgDetatail/index",
				})
			}
		}
	}
</script>

<style>

</style>
