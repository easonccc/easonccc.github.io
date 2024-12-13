---
title: Google登陆
categories: 集成
tags:
  - vue
  - 登录
---

### Google 在 vue3 中的登陆

#### Template

```html
<GoogleLogin
  clientId="项目的clientId"
  :callback="callback"
  class="gooleLogin"
/>
```

#### JS 部分

##### 1. 安装

```
   "vue3-google-login": "^2.0.25"

   npm i vue3-google-login
```

##### 2. 引入

```javascript
import { decodeCredential, GoogleLogin } from "vue3-google-login"
```

##### 3. 点击登录时调用 会回调 callback

```javascript
const callback = (response) => {
  console.log("Handle the response", response)

  handleLogin(response.credential) //调用后端接口

  const userData = decodeCredential(response.credential) // 解析用户信息
  console.log(userData, "userData")
}
```

##### 如果要改 css 样式 直接去改 iframe 里的元素 例如：

```css
<style lang="less">
.nsm7Bb-HzV7m-LgbsSe {
  width: 338px;
}
</style>
```
