<template>
	<view class="content">

		<!-- 分段器 -->
		<view class="category-tabs">
			<view class="category-tabs-title">
				<uni-segmented-control :current="current" :values="items.map(v=>v.title)" @clickItem="onClickItem" style-type="text" active-color="#b4237a"></uni-segmented-control>
			</view>
		</view>
		<!-- 图片列表 -->
		<scroll-view scroll-y="true" class="category-scroll" enable-flex @scrolltolower="hanldeScrolltolower">
			<view class="category-content">
				
				<view class="title_inner"
				v-for="(items,index) in vertical"
				:key="items.id"
				>
					<go-detatail :list="vertical" :index="index">
					<image :src="items.thumb" mode="aspectFill"></image>
					</go-detatail>
				</view>
			</view>
		</scroll-view>
		
		
	</view>
	
</template>

<script>
	import {uniSegmentedControl} from '@dcloudio/uni-ui';
	import goDetatail from "@/components/goDetatail.vue";
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
				hasMore:true,
			}
		},
		onLoad(options) {
			this.id = options.id
			console.log(options)
			this.getList();
		},
		methods: {
			onClickItem(e) {
				
				if (this.current !== e.currentIndex) {
					this.current = e.currentIndex;
				}
				this.params.order = this.items[e.currentIndex].order;
				this.vertical = [];
				this.params.skip = 0;
				this.getList();
			},
			getList() {
				this.request({
					url:'http://service.picasso.adesk.com/v1/vertical/category/'+this.id+'/vertical',
					data:this.params
				}).then(res => {
					console.log(res);
					
					if( res.res.vertical.length  === 0) {
						this.hasMore = false;
						uni.showToast({
							title:'没有更多数据了',
							icon: 'none',
						})
						return;
					} 
					this.vertical = [...this.vertical,...res.res.vertical];
					
				})
			},
			hanldeScrolltolower () {
				if(this.hasMore) {
					this.params.skip += this.params.limit;
					this.getList();
				} else {
					uni.showToast({
						title:'没有更多数据了',
						icon: 'none',
					})
				}
				
			}
		}, 
		components: {
			uniSegmentedControl,
			goDetatail
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
	}
	.category-scroll{
		height: calc(100vh - 74upx);
		.category-content{
			display: flex;
			flex-wrap: wrap;
			.title_inner{
				width: 33%;
				image{
					border: 5upx solid #FFFFFF;
					width: 100%;
				}
			}
		}
	}
	
</style>
