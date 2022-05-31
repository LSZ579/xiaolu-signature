<template>
  <view class="content">
    <xiaolu-signature @getImg="getImg" width="600" height="400"></xiaolu-signature>
  </view>
</template>

<script>
// 新版本hbuilderx支持easycom
// import XiaoluSignature from "../../components/xiaolu-signature/xiaolu-signature.vue"
export default {
  // components: { XiaoluSignature },
  data() {
    return {}
  },
  methods: {
    getImg(e) {
      var pages = getCurrentPages()
      var prevPage = pages[pages.length - 2]
      // prevPage.img = e //小程序写法
      prevPage.$vm.img = e //h5写法
      uni.navigateBack()
    }
  }
}
</script>

<style>
.content {
  display: flex;
  justify-content: center;
}
</style>
