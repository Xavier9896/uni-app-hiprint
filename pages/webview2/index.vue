<template>
  <view class="container">
    <view class="infos">
      <view class="info"
        >连接状态：<view class="state">{{ stateList[state] }}</view>
      </view>
    </view>
    <view class="form">
      <template v-if="state === 'unconnect'">
        <view class="uni-form-item">
          <view class="uni-title">服务地址：</view>
          <uv-input type="text" v-model="form.host" />
        </view>
        <view class="uni-form-item">
          <view class="uni-title">TOKEN：</view>
          <uv-input type="text" v-model="form.token" />
        </view>
      </template>
      <template v-else>
        <view v-if="form.client === ''" class="uni-form-item">
          <view class="uni-title"
            >选择服务：
            <text @click="openPicker('clients')"> 请点击此处选择服务连接 </text>
          </view>
          <uv-picker
            ref="clientsPickerRef"
            :columns="clientsPickerColumns"
            @confirm="handleClientChange"
          ></uv-picker>
        </view>
        <view v-else class="uni-form-item">
          <view class="uni-title">选择的服务：{{ form.client }}</view>
        </view>
        <template v-if="form.client">
          <view v-if="form.printer === ''" class="uni-form-item">
            <view class="uni-title"
              >选择打印机：
              <text @click="openPicker('printer')"> 请点击此处选择打印机 </text>
            </view>
            <uv-picker
              ref="printerPickerRef"
              :columns="printerPickerColumns"
              @confirm="handlePrinterChange"
            ></uv-picker>
          </view>
          <view v-else class="uni-form-item">
            <view class="uni-title">选择的打印机：{{ form.printer }}</view>
          </view>
        </template>
      </template>
    </view>
    <view class="buttons">
      <uv-button
        v-if="state === 'unconnect'"
        type="primary"
        text="连接服务"
        @click="handleConnect"
      >
      </uv-button>
      <uv-button
        :disabled="form.printer === ''"
        :plain="true"
        text="打印"
        @click="handlePrint"
      >
      </uv-button>
			<uv-button
			  :plain="true"
			  text="导出pdf"
			  @click="handlePdf"
			>
			</uv-button>
    </view>
		<!-- 如果需要隐藏, 请在 ↓ web-view 中添加样式 style="height: 0;width: 0;" -->
    <web-view
      ref="hiprint"
			style="height: 0;width: 0;"
			:fullscreen="false"
      src="/static/hiprint/index.html"
      @message="onMessage"
    ></web-view>
  </view>
</template>

<script>
import printJson from "./index.js";
var wv; // webview对象
export default {
  data() {
    return {
      state: "unconnect",
      stateList: {
        unconnect: "未连接",
        connected: "已连接",
      },
      form: {
        host: "https://printjs.cn:17521",
        token: "vue-plugin-hiprint",
        client: "",
        printer: "",
      },
      clients: {},
      clientsPickerColumns: [],
      printerList: [],
      printerPickerColumns: [],
    };
  },
	onReady() {
		// #ifdef APP-PLUS
		var currentWebview = this.$scope.$getAppWebview();
		setTimeout(function () {
		    wv = currentWebview.children()[0];
				// 如果需要隐藏, 请在 ↑ web-view 中添加样式 style="height: 0;width: 0;"
				let info = uni.getWindowInfo();
				wv.setStyle({
						top: info.windowHeight / 3 * 2,
						width: info.windowWidth,
				    height: info.windowHeight / 3
				});
		}, 100);
		// #endif
	},
  methods: {
    /**
     * @description: 触发 uv-picker 打开事件
     * @param {String} type clients | printer
     * @return {Void}
     */
    openPicker(type) {
      this.$refs[`${type}PickerRef`].open();
    },
    /**
     * @description: client 选中确认事件
     * @param {Object} event uv-picker 确认事件
     * @return {Void}
     */
    handleClientChange(event) {
      const client = this.clients.find(
        (client) => event.value[0] === client.machineId
      );
      this.form.client = client?.id;
      this.printerList = client?.printerList;
      const printerPickerColumns = client?.printerList?.map(
        (printer) => printer.name
      );
      // 构建打印机选择 picker 数据
      this.printerPickerColumns = printerPickerColumns?.length
        ? [printerPickerColumns]
        : [];
    },
    /**
     * @description: printer 选中确认事件
     * @param {Object} event uv-picker 确认事件
     * @return {Void}
     */
    handlePrinterChange(event) {
      const printer = this.printerList.find(
        (printer) => event.value[0] === printer.name
      );
      this.form.printer = printer.name;
    },
    /**
     * @description: 通知 webview 连接服务
     * @return {Void}
     */
    handleConnect() {
			console.log('handleConnect ????');
			console.log(wv);
      wv?.evalJS(
        `init("${this.form.host}", "${this.form.token}")`
      );
    },
    /**
     * @description: 通知 webview 打印
     * @return {Void}
     */
    handlePrint() {
      uni.showLoading();
      wv?.evalJS(
        `print({json: ${JSON.stringify(printJson.json)}, data: ${JSON.stringify(
          printJson.data
        )}, options: ${JSON.stringify(this.form)}})`
      );
      // 设置 60s 超时，如果 webview 出错或 中转、客户端 出错未返回，直接关闭 loading
      setTimeout(() => {
        uni.hideLoading();
      }, 60000);
    },
		handlePdf() {
			let options = {isDownload:false};
			let name = 'vue-plugin-hiprint-pdf';
			console.log('handlePdf --- ?????');
			wv?.evalJS(
			  `pdfTest({json: ${JSON.stringify(printJson.json)}, data: ${JSON.stringify(
			    printJson.data
			  )}, name: ${name}, options: ${JSON.stringify(options)}})`
			);
		},
    /**
     * @description: webview 发送过来的数据
     * @param {Object} event webview 消息数据
     * @return {Void}
     */
    onMessage(event) {
      var message = event?.detail.data[0];
      switch (message.type) {
				case "ready":
					console.log('onMessage ready');
					console.log('onMessage ???');
					// this.handleConnect();
					this.handlePdf();
					break;
        case "connected":
          this.state = message.type;
          const clients = JSON.parse(message.data);
          const clientsArray = [];
          const arr = [];
          for (let key in clients) {
            arr.push(clients[key].machineId);
            clientsArray.push({
              ...clients[key],
              id: key,
            });
          }
          this.clients = clientsArray;
          this.clientsPickerColumns = [arr];
          break;
        case "success":
          uni.hideLoading();
          uni.showToast({
            title: "打印成功",
          });
          break;
      }
    },
  },
};
</script>
<style lang="scss">
.infos {
  margin: 16px 0;

  .info {
    display: flex;
    flex-direction: row;
    align-items: center;

    .state {
      display: flex;
      flex-direction: row;
      justify-content: center;
      align-items: center;
    }
  }
}

.form {
  .uni-form-item {
    margin: 10px 0;

    .uni-title {
      margin-bottom: 10px;
    }

    .uni-radio-item {
      display: flex;
      flex-direction: row;
      align-items: center;
      margin: 4px 0;
    }
  }
}

.buttons {
  .uv-button {
    margin: 10px 0;
  }
}
</style>
