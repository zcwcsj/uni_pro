<template>
	<view class="home_category">
		<navigator
			class="cate_item"
			v-for="item in category"
			:key="item.img"
		>
			<image mode="aspectFill" :src="item.img"></image>
			<view class="cate_name">{{item.name}}</view>
		</navigator>
	</view>
</template>

<script>
	export default {
		props: {
      urlobj: Object
    },
		data() {
			return {
				category: []
			}
		},
		mounted() {
			uni.setNavigationBarTitle({
				title: "分类"
			});
			this.getList();
		},
		methods: {
			getList() {
				this.request({
          url: this.urlobj.url
        }).then(result => {
					this.category = result.res;
        })
			}
		}
	}
</script>

<style lang="scss" scoped>
.home_category {
	display: flex;
	flex-wrap: wrap;
	.cate_item {
		width: 33.33%;
		position: relative;
		border: 5rpx solid #ffff;
		image {
			height: 240rpx;
		}
		.cate_name {
			position: absolute;
			width: 100%;
			height: 50rpx;
			left: 0;
			bottom: 0;
			color: #ffffff;
			background-color: linear-gradient(to right top,rgba(0,0,0,0.2), rgba(0,0,0,0));
			font-size: 40rpx;
			display: flex;
			align-items: center;
			padding-left: 20rpx;
		}
	}
}
</style>
