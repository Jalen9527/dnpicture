<template>
	<view class="content">
		
		<!-- 分段器 -->
		<view class="home-tabs">
			<view class="home-tabs-title">
				<uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" style-type="text" active-color="#b4237a"></uni-segmented-control>
			</view>
			
			<view class="title_inner">
				<view v-if="current === 0">
					<video-main></video-main>
				</view>
				<view v-if="current === 1">
					<video-main></video-main>
				</view>
				
				<view v-if="current === 2">
					<video-main></video-main>
				</view>
				<view v-if="current === 3">
					<video-main></video-main>
				</view>
				
				<view v-if="current === 4">
					<video-categroy></video-categroy>
				</view>
				
			</view>
		</view>
		
		<!-- 内容 -->
		<view class="home-tabs-content"></view>
	
	</view>
</template>

<script>
	import videoMain from "./video-main/video-main.vue"
	import videoCategroy from "./video-categroy/video-categroy.vue"
	import {
		uniSegmentedControl
	} from '@dcloudio/uni-ui';
	export default {
		data() {
			return {
				items: [
					{title:"推荐"},
					{title:"娱乐"},
					{title:"最新"},
					{title:"热门"},
					{title:"分类"},
				],
				current: 0
			}
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
			}
		},
		components:{
			uniSegmentedControl,
			videoMain,
			videoCategroy,
		},
		mounted() {
			//修改nav标题
			uni.setNavigationBarTitle({title:"视频"});
		}
	}
</script>

<style lang="scss">
	.home-tabs{
		.home-table-tittle{
			position: relative;
		}
		.home-tabs-title{
			width: 70%;
			margin: 0 auto;
		}
	}
</style>
