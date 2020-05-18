<template>
	<view class="video_paly">
		<image :src="videoObj.img"></image>
		<!-- 工具栏 -->
		<view class="video_tool">
			<view :class="['iconfont', muted ? 'icon-jingyinmute33' : 'icon-volume']" @click="handleMusic"></view>
			<view class="iconfont icon-zhuanfa">
				<button open-type="share"></button>
			</view>
		</view>
		<!-- 视频 -->
		<view class="video_wrap">
			<video :src="videoObj.video" objectFit="fill" :muted="muted"></video>
		</view>
		<!-- 下载 -->
		<view class="download">
			<view class="download_btn" @click="download">下载高清</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				videoObj: {},
				muted: false
			}
		},
		onLoad() {
			this.videoObj = getApp().globalData.video;
		},
		methods: {
			handleMusic() {
				this.muted = !this.muted;
			},
			async download() {
				await uni.showLoading({
					title: "下载中"
				});
				const {tempFilePath} =(await uni.downloadFile({
					url: this.videoObj.video
				}))[1];
				await uni.saveVideoToPhotosAlbum({
					filePath: tempFilePath
				});
				uni.hideLoading();
				uni.showToast({
					title: "下载成功"
				})

			}
		}
	}
</script>

<style lang="scss" scoped>
	.video_paly {
		position: relative;
		image {
			position: absolute;
			width: 100vw;
			width: 100vh;
			z-index: -1;
			filter: blur(20px);
		}
		.video_tool {
			height: 80rpx;
			display: flex;
			justify-content: flex-end;
				.iconfont {
					width: 80rpx;
					color: #ffffff;
					font-size: 50rpx;
					border-radius: 40rpx;
					background-color: rgba(0, 0, 0, 0.2);
					display: flex;
					justify-content: center;
					align-items: center;
					margin-right: 20rpx;
				}
				.icon-zhuanfa {
					position: relative;
					button {
						position: absolute;
						width: 100%;
						height: 100%;
						opacity: 0;
					}
				}
		}
		.video_wrap {
			display: flex;
			justify-content: center;
			video {
				height: 600rpx;
				width: 360rpx;
			}
		}
		.download {
			display: flex;
			justify-content: center;
			margin-top: 30rpx;
			.download_btn {
				width: 360rpx;
				height: 80rpx;
				border-radius: 40rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				color: #fff;
				border: 3rpx solid #fff;
				background-color: rgba(0,0,0,0.6);
			}
		}
	}
</style>
