<template>
	<scroll-view scroll-y="true" class="album_scroll_view" @scrolltolower="handleToLower" >
		<!-- 
			swiper 标签属性
			1 自动轮播 autoplay
			2 指示器 indicator-dots
			3 衔接轮播 circular
		-->
		<view class="album_banner">
			<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000"  circular>
				<swiper-item
					v-for="items in banner"
					:key="items.id"
				>
					<image :src="items.thumb" mode="widthFix"></image>
				</swiper-item>
				
			</swiper>
		</view>
		<!-- 轮播图end -->
		
		<view class="album_list">
			<navigator class="album_item"
				v-for="items in albnm"
				:key="items.id"
				:url="`/pages/home/album_info/album_info?id=${items.id}`"
			>
				<view class="album_img">
					<image :src="items.cover" mode="aspectFill"></image>
				</view>
				
				<view class="album_info">
					<view class="album_name">{{items.name}}</view>
					<view class="album_desc">{{items.desc}}</view>
					<view class="album_but">
						<view class="album_ation">+关注</view>
					</view>
				</view>
				
			</navigator>
		</view>
		
	</scroll-view>
</template>

<script>
	export default {
		data() {
			return {
				params:{
					limit:30,
					order:'new',  //关键字
					skip:0, //跳过几条
				},
				//轮播图数据
				banner:[],
				//列表数组
				albnm:[],
				hasMore:true,
			}
		},
		mounted() {
			//修改nav标题
			uni.setNavigationBarTitle({title:"专辑"});
			this.getList();
			
		},
		methods: {
			getList(){
				this.request({
					url:"http://157.122.54.189:9088/image/v1/wallpaper/album",
					data:this.params
				}).then(res=>{
					
					//判断还有没有数据
					if(res.res.album < this.params.limit) {
						this.hasMore = false;
						return;
					}
					this.banner = res.res.banner;
					this.albnm = [...this.albnm,...res.res.album];
				})
			},
			
			//分页效果
			handleToLower() {
				if(this.hasMore){
					this.params.skip += this.params.limit
					this.getList();
				} else {
					uni.showToast({
						title:"无更多内容",
						icon:'none'
					})
				}
				
			}
		}
	}
</script>

<style lang="scss" scoped>
	.album_scroll_view{
		height: calc(100vh - 66upx);
	}
	.album_banner{
		swiper{
			height: 326upx;
			image{
				height: 100%;
				width: 100%;
			}
		}
	}
	
	.album_list{
		padding: 10upx;
		.album_item{
			display: flex;
			border-bottom: 1upx solid #F2F6FC;
			.album_img{
				flex:  1;
				padding: 10upx 10upx;
				image{
					width: 200upx;
					height: 200upx;
				}
			}
			.album_info{
				flex: 2;
				padding: 0 10upx;
				overflow: hidden;
				.album_name{
					font-size: 30upx;
					color: #000;
					padding: 10upx 0;
				}
				.album_desc{
					padding: 10upx 0;
					font-size: 24upx;
					color: #808080;
					text-overflow: ellipsis;
					overflow: hidden;
					white-space: nowrap;
				}
				.album_but{
					font-size: 26upx;
					padding: 10upx;
					display: flex;
					justify-content: flex-end;
					.album_ation{
						color: $color;
						border: 1upx $color solid;
						padding: 10upx;
						border-radius: 10upx;
					}
				}
			}
		}
	}
</style>
