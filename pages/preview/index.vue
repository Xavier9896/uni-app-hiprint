<template>
  <view class="container">
    <web-view
      ref="hiprint"
      src="/static/hiprint/index.html"
      @message="onMessage"
    ></web-view>
  </view>
</template>

<script>
var wv; // webview窗口对象
import printJson from "./print.js";
export default {
  onReady() {
    console.log("onReady", printJson);
  },
  methods: {
    onMessage(event) {
      var message = event?.detail.data[0];
      switch (message.type) {
        case "ready":
          var currentWebview = this.$scope.$getAppWebview(); //此对象相当于html5plus里的plus.webview.currentWebview()。在uni-app里vue页面直接使用plus.webview.currentWebview()无效
          wv = currentWebview.children()[0];
          wv?.setStyle({ scalable: true });
          uni.showToast({
            title: "已建立webview 通信，开始渲染",
            icon: "none",
            duration: 1500,
          });
          setTimeout(() => {
            wv?.evalJS(
              `preview({json: ${JSON.stringify(
                printJson.json
              )}, data: ${JSON.stringify(printJson.data)}})`
            );
          }, 1500);
          break;
      }
      console.log(event);
    },
  },
};
</script>
