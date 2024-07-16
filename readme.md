# uni-app-hiprint

这是一个 uniapp 开发的 vue-plugin-hiprint 周边生态项目，实现了 uniapp IOS、Android 中的预览、打印。

TODO:

[] 1. 直接使用 socket.io-client 与中转进行通信，不需要依赖 vue-plugin-hiprint
    
    前提：electron-hiprint、node-hiprint-transit 需要改造升级，支持只传输 json、data 进行打印