<template>
	<view class="detatail" >
		<view class="user_info" v-if="ImgDetatil.user">
			<view class="user_avatar">
				<image :src="ImgDetatil.user.avatar" mode="widthFix"></image>
			</view>
			<view class="user_desc">
				<view class="user_name">
					{{ImgDetatil.user.name}}
				</view>
				<view class="user_time">
					{{ImgDetatil.cnTime}}
				</view>
			</view>
		</view>
		<!-- 大图 -->
		<view class="imgDetatail_img">
			<swiper-action @swiperAction="handleSwiperAction">
			<image :src="ImgDetatil.newThumb" mode="widthFix"></image>
			</swiper-action>
		</view>
		
		<!-- 点赞收藏 -->
		<view class="user_rank">
			<view class="rank">
				<uni-icons type="hand-thumbsup"></uni-icons>
				<text class="iconfont ">
				{{ImgDetatil.rank}}</text>
			</view>
			<view class="user_collect">
				<uni-icons type="heart"></uni-icons>
				<text class="iconfont iconcollect">
					收藏</text>
			</view>
		</view>
		
		<!-- 专辑 -->
		<view class="album_warp"
		v-for="items in album"
		:key="items.index"
		v-if="album.length > 0"
		>
			<view class="album_title">
				相关
			</view>
			<view class="album_info">
				<view class="album_img">
					<image :src="items.cover" mode="aspectFill"></image>
				</view>
				<view class="album_desc">
					<text class="album_but">专辑</text>
					<view class="album_name">
						{{items.name}}
					</view>
					<view class="a_desc">
						{{items.desc}}
					</view>
					<view class="album_icon"> <uni-icons type="arrowright"></uni-icons></view>
				</view>
			</view>
		</view>
		
		<!-- 最热评论 -->
		<view class="comment hot_warp" v-if="hot.length>0">
			<view class="comment_title">
				<uni-icons type="spinner-cycle" class="comment_icon" style="color:#d52a7e;"></uni-icons>
				<text class="comment_text">最热评论</text>
			</view>
			<view class="common_item"
			v-for="items in hot"
			:key="items.index"
			>
				<view class="comment_avatar">
					<image :src="items.user.avatar" mode=""></image>
				</view>
				<view class="comment_desc">
					<view class="comment_name">
						<text>{{items.user.name}}</text>
					</view>
					<text class="comment_time">{{items.cnTime}}</text>
					<view class="comment_content">
						<text>{{items.content}}</text>
					</view>
					<view class="comment_collect">
						<uni-icons type="hand-thumbsup" class="collect"></uni-icons>
						<text>{{items.size}}</text>
					</view>
					
				</view>
			</view>
		</view>
		
		<!-- 最新评论 -->
		<view class="comment hot_warp" v-if="comment.length>0">
			<view class="comment_title">
				<uni-icons type="chatboxes" class="comment_icon" style="color: #d52a7e;" font-size="50upx;"></uni-icons>
				<text class="comment_text">最新评论</text>
			</view>
			<view class="common_item"
			v-for="items in comment"
			:key="items.index"
			>
				<view class="comment_avatar">
					<image :src="items.user.avatar" mode=""></image>
				</view>
				<view class="comment_desc">
					<view class="comment_name">
						<text>{{items.user.name}}</text>
					</view>
					<text class="comment_time">{{items.cnTime}}</text>
					<view class="comment_content">
						<text>{{items.content}}</text>
					</view>
					<view class="comment_collect">
						<uni-icons type="hand-thumbsup" class="collect"></uni-icons>
						<text>{{items.size}}</text>
					</view>
					
				</view>
			</view>
		</view>
		
		<!-- 下载 -->
		<view class="download">
			<view class="download_but" @click="handeDownload">下载图片</view>
		</view>
		
	</view>
</template>

<script>
	import swiperAction from "../../components/swiperAction.vue";
	import moment from "moment";
	
	moment.locale("zh-cn");  //设置为中文
	export default {
		components:{ swiperAction },
		data() {
			return {
				ImgDetatil:{},
				album:[],
				comment:[],
				hot:[],
				imgIndex:0,
			}
		},
		onLoad() {
			const {imgIndex} = getApp().globalData;
			this.imgIndex = imgIndex;
			this.getData();
		},
		methods:{
			//当前页面赋值
			getData(){
				const {imgList} = getApp().globalData;
				this.ImgDetatil = imgList[this.imgIndex];
				this.ImgDetatil.newThumb = this.ImgDetatil.thumb;  //+"?imageView2/3/h/360";
				this.ImgDetatil.cnTime = moment(this.ImgDetatil.atime*1000).fromNow();
				
				console.log('===传递过来的数据===');
				console.log(this.ImgDetatil);
				this.getComments(this.ImgDetatil.id);
			},
			
			getComments( id ) {
				this.request({
					url:`https://service.picasso.adesk.com/v2/wallpaper/wallpaper/${id}/comment`
				}).then(result => {
					//数组中的时间处理
					result.res.hot.forEach(v=>v.cnTime = moment(v.atime*1000).fromNow());
					result.res.comment.forEach(v=>v.cnTime = moment(v.atime*1000).fromNow());
					this.album = result.res.album;
					this.comment = result.res.comment;
					this.hot = result.res.hot;
					console.log('===评论接口数据===');
					console.log(result.res);
				})
			},
			//左右滑动处理
			handleSwiperAction(e){
				const {imgList} = getApp().globalData;
				console.log(  'INDEX索引::' + this.imgIndex );
				
				if(e.direction === 'left' && this.imgIndex < imgList.length-1) {
					this.imgIndex ++;
					this.getData();
				} else if( e.direction === 'right' && this.imgIndex >0 ){
					this.imgIndex --;
					this.getData();
				}
			},
			
			//下载图片
			async handeDownload(){
				//获取临时文件
				const result = await uni.downloadFile({url:this.ImgDetatil.img});
				const tempFilePath = result[1].tempFilePath;
				//将临时文件下载到本地中
				const saveRes = await uni.saveImageToPhotosAlbum({ filePath:tempFilePath })
				console.log(saveRes);
				
			}
		}
	}
</script>

<style lang="scss" scoped>
	.detatail{
		margin-bottom: 80upx;
		.user_info{
			display: flex;
			flex-wrap: wrap;
			padding: 0 30upx;
			margin:30upx 0upx;
			.user_avatar{
				width: 16%;
				image{
					width: 88upx;
					height: 50upx;
					border-radius: 50%;
				}
			}
			.user_desc{
				margin-left: 10upx;
				.user_name{
					font-size: 32upx;
					font-weight: 600;
					color: #000;
				}
				.user_time{
					margin-top: 10upx;
					font-size: 30upx;
					color: #909399;
				}
			}
		}
		.imgDetatail_img{
			image{
				width: 100%;
				height: 100%;
			}
		}
		
		.user_rank{
			display: flex;
			font-size: 32upx;
			color: #808080;
			height: 60upx;
			border-bottom: 1upx solid #D3D3D3;
			.rank{
				display: flex;
				justify-content: center;
				align-items: center;
				flex: 1;
			}
			.user_collect{
				display: flex;
				justify-content: center;
				align-items: center;
				flex: 1;
			}
		}
		
		.album_warp{
			border-bottom: #D3D3D3 solid 1upx;
			padding: 10upx;
			.album_title{
				font-size: 30upx;
				padding: 10upx 0;
			}
			.album_info{
				position: relative;
				display: flex;
				.album_img{
					flex: 1;
					image{
						width: 200upx;
						height: 200upx;
					}
				}
				.album_desc{
					flex: 2.3;
					font-size: 30upx;
					overflow: hidden;
					.album_but{
						background-color: $color;
						padding: 5upx 20upx;
						color: #FFFFFF;
						font-size: 30upx;
					}
					.album_name{
						padding: 5upx 0 ;
					}
					.a_desc{
						color: #777777;
						font-size: 28upx;
						text-overflow: ellipsis;
						overflow: hidden;
						white-space: nowrap;
						
					}
					.album_icon{
						position: absolute;
						right: 20upx;
						top: 40upx;
						color: #6E6E6E;
					}
				}
				
			}
		}
		
		.comment{
			
			.comment_title{
				padding: 10upx 10upx;
				.comment_icon{
					color: $color;
					font-size: 32upx;
				}
				.comment_text{
					margin-left: 10upx;
					font-size: 30upx;
					color: #333333;
					font-weight: 600;
				}
			}
			.common_item{
				display:flex;
				position: relative;
				border-bottom: 8upx solid #F2F6FC;
				margin: 20upx 0;
				padding-bottom: 40upx;
				.comment_avatar{
					padding: 10upx;
					flex: 1;
					image{
						width: 100upx;
						height: 100upx;
					}
				}
				.comment_desc{
					flex: 5;
					padding: 10upx;
					.comment_name{
						font-size: 28upx;
						color: #777777;
					}
					.comment_time{
						font-size: 26upx;
						color: #6E6E6E;
					}
					.comment_title{
						font-size: 26upx;
						color: #6E6E6E;
					}
					.comment_content{
						color: #000;
						font-size: 30upx;
					}
					.comment_collect{
						display: flex;
						position: absolute;
						right: 20upx;
						bottom: 10upx;
						.collect{
							flex: 1;
						}
						text{
							font-size: 26upx;
							color: #777777;
							flex: 1;
						}
						
					}
				}
				
			}
		}
		.download{
			position: fixed;
			bottom: 5upx;
			height: 80upx;
			width: 100%;
			display: flex;
			align-items: center;
			justify-content: center;
			.download_but{
				width: 98%;
				height: 90%;
				display: flex;
				align-items: center;
				justify-content: center;
				background-color: $color;
				color: #fff;
				font-size: 35upx;
				font-size: 600;
			}
		}
		
	}
	
	
</style>
