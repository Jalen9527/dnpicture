<template>
	<scroll-view @scrolltolower="handleToLower" class="recommed_view" scroll-y="" v-if="recommeds.length>0">
		<!-- 推荐开始 -->
		<view class="recommed_wrap">
			
			<navigator class="recommed_item"
			v-for="items in recommeds"
			:key="items.id"
			>
			<!-- :url="`/pages/home/album_info/album_info?id=${items.id}`" -->
				<image :src="items.thumb" mode="widthFix"></image>
			</navigator>
			
		</view>
		<!-- 推荐结束 -->
		
		<!-- 月份列表months -->
		<view class="months_warp">
			<view class="months_title">
				<view class="months_title_info">
					<view clas
					s="months_info">
						<text>{{months.DD}}/</text>
						{{months.MM}} 月
					</view>
					<view class="months_text">{{months.title}}</view>
				</view>
				
				<view class="months_title_more">更多></view>
			</view>
			
			<view class="months_content">
				<view class="months_item"
				v-for="(items,index) in months.items"
				:key="items.id"
				>
				<!-- 向组建传递数据 :list 数组   :index 索引 -->
				<go-detatail :list="months.items" :index="index">
					<image :src="items.thumb+items.rule.replace('$<Height>',360)" mode="aspectFill"></image>
				</go-detatail>
				</view>
			</view>
			
		</view>
		<!-- 月份结束 -->
		
		<!-- 热门数据 -->
		<view class="hots_warp" v-if="hots.length > 0">
			<view class="hots_title">
				<text> 热门 </text>
			</view>
			<view class="hots_content">
				<view class="hots_items"
				v-for="(items,index) in hots"
				:key="items.id"
				>
				<go-detatail :list="hots" :index="index">
					<image :src="items.thumb" mode="widthFix"></image>
				</go-detatail>
				</view>
			</view>
		</view>
		<!-- 热门end -->
	</scroll-view>
</template>

<script>
	import moment from "moment";
	import goDetatail from "@/components/goDetatail.vue";
	export default {
		components:{
			goDetatail
		},
		data() {
			return {
				recommeds:[],
				months:{},
				hots:[],
				params:{
					limit:30,
					order:'hot',  //关键字
					skip:0, //跳过几条
				},
				hasMore:true,
			}
		},
		mounted() {
			uni.setNavigationBarTitle({title:"推荐"});
			this.getList();
		},
		methods: {
			
			//获取列表
			getList() {
				this.request({
					url:"http://service.picasso.adesk.com/v3/homepage/vertical",
					data:this.params,
				}).then(res=>{
					
					console.log(res);
					//判断还有没有数据
					if(res.res.vertical.length < this.params.limit){
						this.hasMore = false;
						return ;
					}
					
					//获取数据填充
					if(this.recommeds.length === 0 ){
						this.recommeds = res.res.homepage[1].items;
						this.months = res.res.homepage[2];
						//处理月份
						this.months.MM = moment(this.months.stime).format('MM');
						this.months.DD = moment(this.months.stime).format('DD');
					}
					
					//获取热门数据 es6 写法
					this.hots = [...this.hots,...res.res.vertical];
				})
			},
			
			//滚动条触底时间
			handleToLower(){
				//上滑翻页
				if(this.hasMore){
					this.params.skip += this.params.limit;
					this.getList();
				} else {
					uni.showToast({
						title: '我也是有底线的人...',
						icon:'none'
					});
				}
				
			}
		}
		
	}
</script>

<style lang="scss" scoped>
	.recommed_view{
		//height 屏幕高度 - 头部标题的高度
		height: calc(100vh - 136upx);
	}
	.recommed_wrap{
		display: flex;
		flex-wrap: wrap;
		justify-content:center;
		.recommed_item {
			width: 49%;
			border:3upx solid #FFFFFF;
			image{
				width: 100%;
			}
			float:left;
		}
	}
	.months_warp{
		.months_title{
			display: flex;
			justify-content: space-between;
			padding: 15upx;
			.months_title_info{
				color: $color;
				font-size: 30upx;
				font-weight: 600;
				display: flex;
				.months_info{
					text{
						font-size: 36upx;
					}
				}
				.months_text{
					color: #666666;
					margin-left: 30upx;
				}
			}
			
			.months_title_more{
				font-size: 25upx;
				color: $color;
				line-height: 45upx;
			}
		}
		//content
		.months_content{
			display: flex;
			flex-wrap: wrap;
			justify-content:center;
			.months_item{
				width: 32%;
				border: 5upx solid #FFFFFF;
				image{
					width: 100%;
				}
				float: left;
			}
		}
	}
	.hots_warp{
		.hots_title{
			padding: 20upx;
			text {
				border-left: 10upx solid $color;
				padding-left:10upx ;
				font-size: 30upx;
				font-weight: 600;
			}
		}
		.hots_content{
			display: flex;
			flex-wrap: wrap;
			justify-content:center;
			.hots_items{
				width: 32%;
				// float: left;
				border: 5upx solid #FFFFFF;
				image{
					width: 100%;
				}
			}
		}
	}
</style>
