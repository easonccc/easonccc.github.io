---
title: mac下切换node版本
categories: 配置
tags:
 - 配置
 - node
 - mac
---


### 1、清除node.js的cache
```
sudo npm cache clean -f 
```
### 2、使用npm安装n模块

```
sudo npm install -g n 
```
### 3、查看node所有版本
```
npm view node versions 
```

### 4、升级到最新版本
```
sudo n latest
```

### 升级到稳定版本

```
sudo n stable 
```

### 升级到具体版本号

```
sudo n xx.xx
```


```
node -v //查看当前安装的版本号

n list //检查目前安装了哪些版本的node，会出现已安装的node版本，选一个就可以直接切换了

```

