---
title: 'vue的支付业务（二）：支付宝'
date: 2021-03-05 09:42:44
tags: 'vue支付'
top_img: 'linear-gradient(to right, #eea2a2 0%, #bbc1bf 19%, #57c6e1 42%, #b49fda 79%, #7ac5d8 100%)'
cover: false
copyright_url: https://hase.org.cn/2021/03/04/mybo3/
copyright_info: 转载请注明来自 Hase！
---

这里记录了使用vue技术栈，包括微信、微信小程序、支付宝、stripe、蓝新、eghl等支付业务的代码

## vue支付宝支付

注意：支付宝支付是不支持在微信内部打开的，所以在微信中需要引导用户去浏览器打开
上码！！
```
// 创建div
const div = document.createElement('div')
//塞入ID
div.setAttribute('id', 'myAliPay'); 
// 将后台处理好的form 放入div
div.innerHTML = res.data.data.str 
//将div塞入dom
document.body.appendChild(div)
//将div里面的form表单进行提交
document.getElementById('myAliPay').getElementsByTagName('form')[0].submit()
```
要是微信的支付也能和支付宝一样简单就好了！
有问题请直接加我QQ（邮箱）探讨。
