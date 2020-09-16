<template>
	<view>
		<!-- 专辑背景 -->
		<view class="album_bg" v-if=" Object.keys(album).length > 0">
			<image :src="album.lcover" mode="widthFix"></image>
			<view class="album_info">
				<view class="album_info_title">
					{{ album.name }}
				</view>
				<view class="album_info_but">
					<text>关注专辑</text>
				</view>
			</view>
		</view>
		
		<!-- 专辑作者 -->
		<view class="album_author" v-if=" Object.keys(album).length > 0">
			<view class="album_author_info">
				<view class="author_avatar">
					<image :src="album.user.avatar" mode="widthFix"></image>
				</view>
				<view class="author_name">{{ album.user.name }}</view>
			</view>
			<view class="album_author_desc">
				<text>{{ album.desc }}</text>
			</view>
		</view>
		
		<!-- 列表开始 -->
		<view scroll-y="true" class="album_list" v-if="wallpaper.length > 0" >
			<view class="album_list_img"
			v-for="(items,index) in wallpaper"
			:key="items.id"
			>
				<go-detatail :list="wallpaper" :index="index">
					<image 
					:src="items.thumb+items.rule.replace('$<Height>',360)" 
					mode="aspectFill"></image>
				</go-detatail>
			</view>
		</view>
	</view>
</template>

<script>
	import goDetatail from "@/components/goDetatail.vue";
	export default {
		components:{goDetatail},
		data() {
			return {
				params:{
					limit:30,
					order:"new",
					skip:0,
					// first = 1返回数组中带有 album数组， 
					// first = 0时候只有wallpaper 用于上拉数据提取
					first:1
				},
				id:-1,
				album:{},
				wallpaper:[],
				hasMore:true,
			}
		},
		onLoad(options) {
			this.id = options.id;
			// this.id = "5d5f8e45e7bce75ae7fb8278"; 
			this.getList();
		},
		methods: {
			getList(){
				this.request({
					url:"http://157.122.54.189:9088/image/v1/wallpaper/album/"+this.id+"/wallpaper",
					data:this.params
				}).then(res=>{
					console.log(res);
					if(Object.keys(this.album).length === 0){
						this.album = res.res.album;
					}
					
					if(res.res.wallpaper.length === 0  && this.wallpaper.length > 0) {
						this.hasMore = false;
						this.showNone();
						return ;
					}
					
					this.wallpaper = [...this.wallpaper,...res.res.wallpaper];
				})
			},
			showNone(){
				uni.showToast({
					title:"没有更多数据...",
					icon:"none"
				})
			}
		},
		onReachBottom() {
			if(this.hasMore) {
				this.params.first = 0;
				this.params.skip += this.params.limit;
				this.params.first = 0;
				this.getList();
			}else{
				this.showNone();
			}
		}
	}
</script>

<style lang="scss" scoped>
	.album_bg{
		position: relative;
		image{
			width: 100%;
		}
		.album_info{
			position: absolute;
			width: 100%;
			bottom: 10upx;
			padding: 20upx;
			display: flex;
			justify-content:space-around;
			color: #FFFFFF;
			overflow: hidden;
			align-items: center;
			.album_info_title{
				width: 65%;
				font-size: 40upx;
				text-overflow: ellipsis;
				overflow: hidden;
				white-space: nowrap;
			}
			.album_info_but{
				width: 20%;
				display: flex;
				text{
					font-size: 30upx;
					background-color: $color;
					padding: 10upx;
					border-radius: 10upx;
				}
			}
		}
	}
	//专辑作者style
	.album_author{
		padding: 10upx 20upx;
		.album_author_info{
			display: flex;
			flex-wrap: wrap;
			.author_avatar{
				width:80upx ;
				image{
					width: 100%;
					border-radius: 50%;
				}
			}
			.author_name{
				line-height: 66upx;
				margin-left: 20upx;
				font-weight: 700;
				font-size: 30upx;
			}
		}
		.album_author_desc{
			// padding: 10upx;
			font-size: 30upx;
			color: #555555;
		}
	}
	.album_list{
		display: flex;
		flex-wrap: wrap;
		justify-content: start;
		.album_list_img{
			width: 32%;
			height: 140upx;
			border: 5upx solid #FFFFFF;
			image{
				width: 100%;
				height: 140upx;
			}
		}
	}
</style>
