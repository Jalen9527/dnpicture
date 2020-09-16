<template>
	<view class="content">
		
		<!-- 分段器 -->
		<view class="category-tabs">
			<view class="category-tabs-title">
				<uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" style-type="text" active-color="#b4237a"></uni-segmented-control>
			</view>
			
			<view class="title_inner"
			v-for="items in vertical"
			:key="index"
			>
				<image :src="items.thumb" mode="widthFix"></image>
			</view>
		</view>
		
	</view>
	
</template>

<script>
	import {uniSegmentedControl} from '@dcloudio/uni-ui';
	export default {
		data() {
			return {
				id:0,
				items: [
					{title:"最新", order:'new'},
					{title:"热门", order:'hot'},
				],
				current: 0,
				params: {
					limit: 30,
					skip: 0,
					order: 'new',
				},
				vertical:[],
			}
		},
		onLoad(options) {
			this.id = options.id
			console.log(options)
			this.getList();
		},
		methods: {
			onClickItem(e) {
				this.params.order = this.items[e.currentIndex].order;
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
				this.getList();
			},
			getList() {
				this.request({
					url:'http://157.122.54.189:9088/image/v1/vertical/category/'+this.id+'/vertical',
					data:this.params
				}).then(res => {
					console.log(res);
					this.vertical = res.res.vertical;
				})
			}
		}, 
		components: {
			uniSegmentedControl,
		},
	}
</script>

<style lang="scss">
	.category-tabs{
		display: flex;
		flex-wrap: wrap;
		justify-content: center;
		.category-table-tittle{
			position: relative;
		}
		.category-tabs-title{
			width: 70%;
			margin: 0 auto;
		}
		.title_inner{
			width: 32%;
			padding: 5upx;
			image{
				width: 100%;
			}
		}
	}
</style>
