# App 与微信小程序交互

### 背景
App 内嵌缴费 H5 页面，H5 点击缴费通过原生的方法 `navigateToMiniProgram` 跳转到小程序进行支付。
支付完成后，小程序需又返回到 App。小程序在返回 App 时携带一个支付标识 （支付成功or支付失败） ①成功时：paySucceedStatus：true ； ②失败时：paySucceedStatus：false 。
最后 App 获取该支付状态后再传给内嵌 H5 页面。

### 关键
这里的关键操作是小程序如何返回到 App 中。这里需要注意的是小程序使用的是 `WXEntryActivity`，而微信支付使用的是 `WXPayEntryActivity`，名称不对是无法实现返回的。
另外就是 `WXEntryActivity` 的位置与 `WXPayEntryActivity` 一样有要求的，需和应用程序的包名挂钩，即：`android:name="${applicationId}.wxapi.WXEntryActivity"`，applicationId 为应用程序的包名，例如：`com.example.app`。

具体的实现方式可参考：
1. 小程序与 App 交互可参考：
https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/launchApp.html
https://developers.weixin.qq.com/doc/oplatform/Mobile_App/Access_Guide/Android.html

2，App 与内嵌 H5 交互，可使用 JavascriptInterface。比如：`webView.loadUrl("javascript:PayStatusCallJS(${resCode})")`
