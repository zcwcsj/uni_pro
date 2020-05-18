<template>
  <view @click="handleClick"> 
    <slot></slot>
  </view>
</template>

<script>
export default {
  props: {
    list: Array,
    index: Number
  },
  methods: {
    handleClick() {
      //缓存全局数据  imgList图片列表 imgIndex图片索引
      getApp().globalData.imgList = this.list;
      getApp().globalData.imgIndex = this.index;
      uni.navigateTo({
        url: "/pages/imgDetail/index"
      })
    }
  }
}
</script>

<style scoped>

</style>
