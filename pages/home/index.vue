<template>
	<view class="content">
		
		<!-- 分段器 -->
		<view class="home-tabs">
			<view class="home-tabs-title">
				<uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" style-type="text" active-color="#b4237a"></uni-segmented-control>
			</view>
			
			<view class="title_inner">
				<view v-if="current === 0">
					<homeRecommed></homeRecommed>
				</view>
				<view v-if="current === 1">
					<homeCategory></homeCategory>
				</view>
				
				<view v-if="current === 2">
					<homeNew></homeNew>
				</view>
				<view v-if="current === 3">
					<homeAlbum></homeAlbum>
				</view>
			</view>
			
			<view class="iconfont iconsearch">
				<uni-icons type="search" color='#8f8f94' size="25" />
			</view>
		</view>
		
		<!-- 内容 -->
		<view class="home-tabs-content"></view>

	</view>

</template>

<script>
	import homeAlbum from "./album/album.vue";
	import homeCategory from "./category/category.vue";
	import homeNew from "./new/new.vue";
	import homeRecommed from "./recommed/recommed.vue";
	import {
		uniSegmentedControl
	} from '@dcloudio/uni-ui';

	export default {
		data() {
			return {
				items: [
					{title:"推荐"},
					{title:"分类"},
					{title:"最新"},
					{title:"专辑"},
				],
				current: 0
			}
		},
		onLoad() {
			
		},
		methods: {
			onClickItem(e) {
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
			}
		},
		components: {
			homeAlbum,
			homeCategory,
			homeNew,
			homeRecommed,
			uniSegmentedControl,
		},
		mounted() {
			//修改nav标题
			uni.setNavigationBarTitle({title:"推荐"});
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
		.iconsearch{ 
			position: absolute;
			right: 5px;
			top: 5px;
		}
	}
</style>
