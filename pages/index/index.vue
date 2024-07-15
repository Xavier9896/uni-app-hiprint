<template>
  <view class="container">
    <view class="info">
      这是一个 uniapp 开发的 vue-plugin-hiprint 周边生态项目，实现了 uniapp
      IOS、Android 中的打印。
    </view>
    <uv-button type="primary" text="进入打印 webView 页" @click="navigator"></uv-button>
  </view>
</template>

<script>
export default {
  data() {
    return {};
  },
  onLoad() {},
  methods: {
    navigator() {
      uni.navigateTo({
        url: "/pages/webview/index",
      });
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  box-sizing: border-box;
  color: $uv-main-color;
}
.info {
  margin: 16px 0;
}
</style>
