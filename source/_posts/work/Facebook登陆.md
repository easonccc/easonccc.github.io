---
title: Facebook登陆
categories: 集成
tags:
  - vue
  - 登录
---

### Facebook 在 vue3 中的登陆

#### Template

```html
<div @click="onFBLogin">Facebook登陆</div>
```

#### JS 部分

1.  先初始化

```javascript
const loadFBSDK = () => {
  ;(function (d, s, id) {
    // Load the SDK asynchronously
    var js
    var fjs = d.getElementsByTagName(s)[0]
    if (d.getElementById(id)) return
    js = d.createElement(s)

    js.src = "https://connect.facebook.net/en_US/sdk.js"
    fjs.parentNode.insertBefore(js, fjs)
  })(document, "script", "facebook-jssdk")

  window.fbAsyncInit = function () {
    FB.init({
      appId: "填入项目的appId",
      xfbml: true,
      version: "v20.0",
    })
  }
}

onMounted(() => {
  loadFBSDK()
})
```

2.  点击登录时调用 onFBLogin

```javascript
const onFBLogin = () => {
  FB.login(function (response) {
    if (response.authResponse) {
      console.log(response, "response")
      handleLogin(response.authResponse.accessToken) // 调用后端接口登录

      FB.api("/me", function (response) {
        console.log("Good to see you, " + response.name + ".")
      })
    } else {
      console.error(response)
      console.log("User cancelled login or did not fully authorize.")
    }
  })
}
```
