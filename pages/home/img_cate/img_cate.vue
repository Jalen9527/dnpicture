<template>
	<view>
		图片分类
	</view>
</template>

<script>
	export default {
		data() {
			return {
				id:0,
			}
		},
		onLoad(options) {
			this.id = options.id
			console.log(options)
		},
		methods: {
			
		}
	}
</script>

<style>

</style>
