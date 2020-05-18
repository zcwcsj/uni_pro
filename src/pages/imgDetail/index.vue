<template>
	<view>
		<!-- 用户信息 -->
		<view class="user_info">
			<view class="user_icon">
				<image :src="imgDetail.user.avatar" mode="widthFix"></image>
			</view>
			<view class="user_desc">
				<view class="user_name">{{imgDetail.user.name}}</view>
				<view class="user_time">{{imgDetail.cnTime}}</view>
			</view>
		</view>
		<!-- 高清大图 -->
		<view class="high_img">
			<swiper-action @swiperAction="handleSwiperAction">
				<image mode="aspectFill" :src="imgDetail.thumb"></image>
			</swiper-action>
		</view>
		<!-- 点赞 -->
		<view class="user_rank">
			<view class="rank">
				<text class="iconfont icon-dianzan">{{imgDetail.rank}}</text>	
			</view>
			<view class="user_collect">
				<text class="iconfont icon-shoucang">收藏</text>	
			</view>
		</view>
		<!-- 专辑开始 -->
		<view class="album_wrap" v-if="album.length">
			<!-- 标题 -->
			<view class="album_title">相关</view>
			<view class="album_list">
				<view class="album_item"
					v-for="item in album"
					:key="item.id"
				>
				<!-- 左边图片 -->
					<view class="album_cover">
						<image :src="item.cover" mode="aspectFill"></image>
					</view>
					<!-- 右边 -->
					<view class="album_info">
						<view class="album_info_text">专辑</view>
						<view class="album_name">{{item.name}}</view>
						<view class="iconfont icon-next"></view>
					</view>
				</view>
			</view>
		</view>
		<!-- 最热评论 -->
		<view class="comment hot" v-if="hot.length">
			<view class="comment_title">
					<text class="iconfont icon-remen"></text>
					<text class="comment_text">最热评论</text>
			</view>
			<view class="comment_list">
				<view class="comment_item"
					v-for="item in hot"
					:key="item.id"
				>
					<!-- 用户信息 -->
					<view class="comment_user">
						<!-- 用户头像 -->
						<view class="user_icon">
							<image :src="item.user.avatar" mode="widthFix"></image>
						</view>
						<!-- 用户名称 -->
						<view class="user_name">
							<view class="user_nickname">{{item.user.name}}</view>
							<view class="user_time">{{item.cnTime}}</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user_badge">
							<image
								v-for="item2 in item.user.title"
								:key="item2.icon"
								:src="item2.icon"
							></image>
						</view>
					</view>
					<!-- 评论数据 -->
					<view class="comment_desc">
						<view class="comment_content">{{item.content}}</view>
						<view class="comment_like">
							<text class="iconfont icon-dianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 最新评论 -->
		<view class="comment new"  v-if="comment.length">
			<view class="comment_title">
					<text class="iconfont icon-remen"></text>
					<text class="comment_text">最新评论</text>
			</view>
			<view class="comment_list">
				<view class="comment_item"
					v-for="item in comment"
					:key="item.id"
				>
					<!-- 用户信息 -->
					<view class="comment_user">
						<!-- 用户头像 -->
						<view class="user_icon">
							<image :src="item.user.avatar" mode="widthFix"></image>
						</view>
						<!-- 用户名称 -->
						<view class="user_name">
							<view class="user_nickname">{{item.user.name}}</view>
							<view class="user_time">{{item.cnTime}}</view>
						</view>
						<!-- 用户徽章 -->
						<view class="user_badge">
							<image
								v-for="item2 in item.user.title"
								:key="item2.icon"
								:src="item2.icon"
							></image>
						</view> 
					</view>
					<!-- 评论数据 -->
					<view class="comment_desc">
						<view class="comment_content">{{item.content}}</view>
						<view class="comment_like">
							<text class="iconfont icon-dianzan">{{item.size}}</text>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 图片下载 -->
		<view class="download">
			<view class="download_btn" @click="handleDownload">
				下载图片
			</view>
		</view>
	</view>
</template>

<script>
	import moment from 'moment';
	import swiperAction from "@/components/swiperAction";
	moment.locale("zh-cn");
	export default {
		components: {
			swiperAction
		},
		data() {
			return {
				imgDetail: {},
				imgIndex: 0,
				//专辑数据
				album: [],
				//最热评论
				hot: [],
				//最新评论
				comment: []
			}
		},
		onLoad() {
			const {imgIndex} = getApp().globalData;
			this.imgIndex = imgIndex;
			this.getData();
		},
		methods: {
			getData() {
				const {imgList} = getApp().globalData;
				this.imgDetail = imgList[this.imgIndex];
				//格式化时间
				this.imgDetail.cnTime = moment(this.imgDetail.atime*1000).fromNow();
				//获取图片的详情id this.imgDetail.id
				this.getCommennts(this.imgDetail.id);
			},
			getCommennts(id) {
					this.request({
						url: `http://157.122.54.189:9088/image/v2/wallpaper/wallpaper/${id}/comment`
				}).then(result => {
					this.album = result.res.album;
					result.res.hot.forEach(v => {
						v.cnTime = moment(v.atime*1000).fromNow();
					});
					this.hot = result.res.hot;
					result.res.comment.forEach(v => {
						v.cnTime = moment(v.atime*1000).fromNow();
					});
					this.comment = result.res.comment;

				})
			},
			handleSwiperAction(e) {	
				const {imgList} = getApp().globalData;
				if (e.direction === "left" && this.imgIndex < (imgList.length - 1)) {
					this.imgIndex++;
					this.getData();
				} else if(e.direction === "right" && this.imgIndex > 0){
					this.imgIndex--;
					this.getData();
				} else {
					uni.showToast({
						title: "没有数据了",
						icon: "none"
					})
				}
			},
			async handleDownload() {
				await uni.showLoading({
					title: "下载中"
				})	
				let result = await uni.downloadFile({
					url: this.imgDetail.img
				});
				const {tempFilePath} = result[1];
				let res = await uni.saveImageToPhotosAlbum({
					filePath: tempFilePath
				});
				uni.hideLoading();
				await uni.showToast({
					title: "下载完成",
					icon: "none"
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.user_info {
		display: flex;
		padding: 20rpx;
		.user_icon {
			padding: 0 20rpx;
			image {
				width: 88rpx;
				border-radius: 50%;
				height: 88rpx;
			}
		}
		.user_desc {
			.user_name {
				color: #000;
				font-weight: 600;
			}
			.user_time {
				color: #ccc;
				padding: 10rpx 0;
				font-size: 24rpx;
			}
		}
	}
	.user_rank {
		display: flex;
		height: 80rpx;
		border-bottom: 5rpx solid #ccc;
		.rank {
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
		}
		.user_collect {
			flex: 1;
			display: flex;
			justify-content: center;
			align-items: center;
		}
	}
	.album_wrap {
		padding: 20rpx;
		.album_title {
			padding: 10rpx 0;
		}
		.album_list {
			.album_item {
				display: flex;
				padding: 10rpx 0;
				border-bottom: 10rpx solid #cccccc;
				.album_cover {
					flex: 1;
					image {
						width: 180rpx;
						height: 180rpx;
					}
				}
				.album_info {
					flex: 3;
					padding-left: 20rpx;
					position: relative;
					.album_info_text {
						width: 100rpx;
						height: 50rpx;
						background-color: $color;
						color: #fff;
						display: flex;
						justify-content: center;
						align-items: center;
					}
					.album_name {
						padding: 10rpx 0;
						color: #888;
					}
					.iconfont {
						font-size: 40rpx;
						position: absolute;
						top: 50%;
						transform: translateX(-50%);
						right: 10%;
						color: #000;
					}
				}
			}
		}
	}
	.comment {
		.comment_title {
			padding: 15rpx;
			.iconfont {
				color: red;
				font-size: 40rpx;
			}
			.comment_text {
				font-weight: 600;
				font-size: 28rpx;
				color: #666;
				margin-left: 10rpx;
			}
		}
		.comment_list {
			.comment_item {
				border-bottom: 15rpx solid #ccc;
				.comment_user {
					display: flex;
					padding: 20px 0;
					.user_icon {
						width: 15%;
						display: flex;
						justify-content: center;
						align-items: center;
						image {
							width: 90%;
						}
					}
					.user_name {
						flex: 1;
						.user_nickname {
							color: #777;
						}
						.user_time {
							color:#cccccc;
							font-size: 24rpx;
							padding: 5rpx;
						}
					}
					.user_badge {
						image {
							width: 40rpx;
							height: 40rpx;
							float: right;
							margin-right: 8rpx;
						}
					} 
				}
				.comment_desc {
					display: flex;
					padding: 30rpx 0;
					.comment_content {
						flex: 1;
						padding-left: 15%;
						color: #000;
					}
					.comment_like {
						text-align: right;
					}
				}
			}
		}
	}
	.high_img {
		image {
			border-bottom: 1rpx solid #eee;
			height: 360rpx;
		}
	}
	.new {
		.iconfont.icon-remen {
			color: aqua !important;
		}
	}
	.download {
		height: 120rpx;
		display: flex;
		align-items: center;
		justify-content: center;
		.download_btn {
			width: 80%;
			height: 85%;
			background-color: $color;
			color: #fff;
			font-size: 50rpx;
			font-weight: 600;
			display: flex;
			justify-content: center;
			align-items: center;
		}
	}
</style>
