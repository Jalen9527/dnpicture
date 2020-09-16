<template>
	<view
	@touchstart="handleTouchStart"
	@touchend="handleTouchEnd"
	>
		<slot></slot>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				touches:{
					startTime:0,
					startX:0,
					startY:0,
				},
			};
		},
		methods:{
			handleTouchStart(event) {
				this.touches.startTime = Date.now();
				this.touches.startX = event.changedTouches[0].clientX;
				this.touches.startY = event.changedTouches[0].clientY;
			},
			handleTouchEnd(event) {
				const endTime = Date.now();
				const endX = event.changedTouches[0].clientX;
				const endY = event.changedTouches[0].clientY;
				if( endTime - this.touches.startTime > 2000 ){
					return ;  //超过两秒不执行
				}
			
				//定义滑动的方向
				let direction="";
				
				//判读是否是合法的左右滑动
				if( Math.abs( endX - this.touches.startX) > 50) {
					//判断是左滑还是右滑
					direction = endX - this.touches.startX > 0 ? 'right' : 'left';
				} else {
					return ;
				}
				//将参数传递给父组件
				this.$emit("swiperAction",{direction});
			}
		}
	}
</script>

<style>

</style>
