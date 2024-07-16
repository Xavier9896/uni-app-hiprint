<template>
  <view class="container">
    <view class="info">
      这是一个 uniapp 开发的 vue-plugin-hiprint 周边生态项目，实现了 uniapp
      IOS、Android 中的打印。
    </view>
    <view class="buttons">
      <uv-button
        type="primary"
        text="进入 打印 页"
        @click="navigator('print')"
      ></uv-button>
      <uv-button
        type="primary"
        text="进入 预览 页"
        @click="navigator('preview')"
      ></uv-button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {};
  },
  onLoad() {},
  methods: {
    navigator(type) {
      var url = "";
      switch (type) {
        case "print":
          url = "/pages/webview/index";
          break;
        case "preview":
          url = "/pages/preview/index";
          break;
      }
      uni.navigateTo({
        url,
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
.buttons {
  display: flex;
  flex-direction: column;
  gap: 10px;
}
</style>
