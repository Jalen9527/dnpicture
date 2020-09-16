<template>
	<view class="home_cate">
		<navigator class="cate_item"
		v-for="items in category"
		:key="items.id"
		:url="`/pages/home/img_cate/img_cate?id=${items.id}`"
		>
			<image :src="items.cover" mode="aspectFill"></image>
			<view class="cate_name">
				<text>{{items.name}}</text>
			</view>
			
		</navigator>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				category:[]
			}
		},
		mounted() {
			//修改nav标题
			uni.setNavigationBarTitle({title:"分类"});
			
			//获取列表
			this.getList();
		},
		methods: {
			getList(){
				this.request({
					 //url:'http://service.picasso.adesk.com/v1/vertical/category'
					 url:'http://157.122.54.189:9088/image/v1/vertical/category'
				}).then(res=>{
					console.log(res);
					this.category = res.res.category;
				})
			}
			
		}
	}
</script>

<style lang="scss" scoped>
.home_cate{
	display: flex;
	flex-wrap: wrap;
	justify-content: start;
	width: 100%;
	.cate_item{
		margin-left: 3upx;
		width: 32%;
		position: relative;
		padding:  5upx;
		image{
			width: 100%;
			height: 240upx;
		}
		.cate_name{
			position: absolute;
			bottom: 5upx;
			padding: 10upx 20upx;
			text{
				font-size: 30upx;
				color: #FFFFFF;
			}
		}
	}
}
</style>
