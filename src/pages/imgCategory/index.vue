<template>
  <view>
			<view class="category_tab">
				<view class="category_tab_title">
					<view class="title_inner">
						<uni-segmented-control 
							:current="current"
							:values="items.map(v=>v.title)"
							@clickItem="onClickItem"
							style-type="text"
							active-color="#d4237a"
						></uni-segmented-control>
					</view>
					<view class="iconfont icon-search"></view>
				</view>
				<scroll-view scroll-y enable-flex @scrolltolower="handleScrollTolower" class="category_tab_content">
						<view class="cate_item"
              v-for="(item, index) in vertical"
              :key="item.id"
            >
            <go-detail :list="vertical" :index="index">
              <image :src="item.thumb" mode="widthFix"></image>
            </go-detail>
            </view>
				</scroll-view>
			</view>
	</view>
</template>

<script>
import {uniSegmentedControl} from "@dcloudio/uni-ui";
import goDetail from '@/components/goDetail';
export default {
  components: {
    uniSegmentedControl,
    goDetail
	},
  data() {
    return {
      items: [
        {
          "title": "最新",
          "order": "new"
        },
        {
          "title": "热门",
          "order": "hot"
        }
      ],
      current: 0,
      params: {
        limit: 30,
        skip: 0,
        order: "new"
      },
      id: 0,
      vertical: [],
      hasMore: true
    }			
  },
  onLoad(options) {
    this.id = options.id;
    this.getList();
  },
  methods: {
    onClickItem(e) {
      if(this.current !== e.currentIndex) {
        this.current = e.currentIndex;
        this.params.order = this.items[e.currentIndex].order;
        this.params.skip = 0;
        this.vertical = [];
        this.getList();
      }
    },
    getList() {
      this.request({
        url: `http://157.122.54.189:9088/image/v1/vertical/category/${this.id}/vertical`,
        data: this.params
      }).then(result => {
        if (result.res.vertical.length === 0) {
          this.hasMore = false;
          return;
        }
        this.vertical = [...this.vertical , ...result.res.vertical];
      })
    },
    handleScrollTolower() {
      console.log(11111);
      if (this.hasMore) {
        this.params.skip += this.params.limit;
        this.getList();
      } else {
        uni.showToast({
          "title": "没有数据了",
          "icon": "none"
        })
      }
    }
  }
}
</script>

<style lang="scss" scoped>
	.category_tab {
		.category_tab_title {
			position: relative;
			.title_inner{
				width: 60%;
				margin: 0 auto;
			}
			.icon-search {
				position: absolute;
				top: 50%;
				transform: translateY(-50%);
				right: 5%;
			}
		}
	}
  .category_tab_content {
    height: calc(100vh - 36px);
    display: flex;
    flex-wrap: wrap;
    .cate_item {
     width: 33.33%;
     border: 5rpx solid #fff;
    }
  }
</style>