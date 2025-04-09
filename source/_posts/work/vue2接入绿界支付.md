---
title: vue2接入绿界支付
categories: 第三方
tags:
  - vue
  - 接入第三方
---

## 实现的功能

vue2接入第三方绿界支付

#### Template

```html
 <!-- 支付画面容器 -->
 <div id="ECPayPayment" v-show="!loading"></div>
<!-- 填写完绿界的页面之后点击确定 -->
 <div class="submit_btn" @click="submitBtn">提交</div>
```

#### JS 部分

```javascript
    // 在onMounted或者onLoad里调用
   this.initEcPay();

    // 初始化
    initEcPay() {
      // 引入jQuery
      const scriptJQuery = document.createElement("script");
      scriptJQuery.src = "https://code.jquery.com/jquery-3.5.1.min.js";
      scriptJQuery.onload = () => {
        // 引入绿界支付SDK
        const scriptECPay = document.createElement("script");
        scriptECPay.src =
          "https://ecpg.ecpay.com.tw/Scripts/sdk-1.0.0.js?t=20210121100116";
        scriptECPay.onload = () => {
          // 初始化SDK 测试为Prod
          ECPay.initialize("Stage", 1, (errMsg) => {
            if (errMsg) {
              console.error("SDK初始化失败:", errMsg);
            } else {
              console.log("SDK初始化成功");
              nextTick(() => {
                this.callECPay(this.token);
              });
            }
          });
        };
        document.body.appendChild(scriptECPay);
      };
      document.body.appendChild(scriptJQuery);
    },
    // 调用绿界支付界面 点击确定（自己页面添加确定按钮）
    callECPay(Token) {
      // 设置语言
      const language = ECPay.Language.zhTW;
      // 创建支付画面
      ECPay.createPayment(
        Token,
        language,
        (errMsg) => {
          if (errMsg) {
            console.error("创建支付画面失败:", errMsg);
          } else {
            console.log("支付画面创建成功");
            this.loading = false;
            uni.hideLoading();
          }
        },
        "V2"
      );
    },
    // 提交，结合后端服务完成支付
    submitBtn() {
      var _this = this;
      ECPay.getPayToken(function (paymentInfo, errMsg) {
        console.log(paymentInfo, errMsg, "paymentInfo");
        if (errMsg != null) {
          uni.showToast({
            title: errMsg,
            icon: "none", // success/warning/error
            duration: 2000, // 可选，单位为是秒，就以为2000
          });
          return;
        }

        _this
          .myHttp(
            "/app-api/pay/ecpay/createPayment",
            {
              id: _this.payOrderId,
              channelCode: _this.channelCode,
              payToken: paymentInfo.PayToken,
              merchantTradeNo: paymentInfo.MerchantTradeNo,
            },
            "post",
            false
          )
          .then((result) => {
            console.log(result, "result");
            if (result.displayContent) {
              setTimeout(() => {
                window.open(result.displayContent, "_blank");
              }, 10);
              uni.reLaunch({
                url: "/pages/order/order?tabIndex=0",
              });
            }
          });

        // $("#PayToken").val(paymentInfo.PayToken);

        // return true;
      });
    },

```
