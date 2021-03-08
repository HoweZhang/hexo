---
title: 'vue的支付业务（三）：其他支付'
date: 2021-03-08 10:25:28
tags: 'vue支付'
top_img: 'linear-gradient(to right, #eea2a2 0%, #bbc1bf 19%, #57c6e1 42%, #b49fda 79%, #7ac5d8 100%)'
cover: false
copyright_url: https://hase.org.cn/2021/03/04/mybo4/
copyright_info: 转载请注明来自 Hase！
---

这里记录了使用vue技术栈，包括微信、微信小程序、支付宝、stripe、蓝新、eghl等支付业务的代码

## stripe
stripe支付是香港地区使用较多的支付方式之一
安装它的ui组件[vue-stripe-elements-plus](https://www.npmjs.com/package/vue-stripe-elements-plus)
```
npm i vue-stripe-elements-plus --save
```
需要在index.html中放入
```
<script src="https://js.stripe.com/v3/"></script>
```
在你的组件里使用
```
<template>
  <card
    class='stripe-card'
    :class='{ complete }'
    :stripe='publishableKey'
    :successUrl="successUrl"
    @change='complete = $event.complete'
  />
</template>
<script>
  import {Card, createToken, confirmPaymentIntent  } from 'vue-stripe-elements-plus'
  export default {
    components:{
      Card,
    },
    data() {
      return {
        publishableKey:'key',//可以选择正式环境和测试环境
        items: [
          {
            sku: '',//需要请求后台，拿到后台返回的sign
            quantity: 1
          }
        ],
        successUrl: '',//支付成功后跳转地址
        cancelUrl: '',//支付失败后跳转地址
      }
    },
    methods:{
      stripePay(){
        //请求后台拿到sign后发起支付
        confirmPaymentIntent(sign).then((data) => {
          if(data.error){
            this.$message({
              message: '輸入信息有誤！',
              type: 'error'
            });
          }else{
              console.log("success")
          }
      })
      }
    },
  }
</script>
```

## 蓝新
蓝新支付是台湾地区使用较多的支付方式之一
上码
```
<!--==蓝新支付==-->
<form name="tw_pay" method="post" action="这里放测试环境或者正式环境的地址" hidden id="tw_form"> 
  //twPayObjArr是从后台返回的，支付的相关属性
  <input type="text" v-for="item in twPayObjArr" :name="item.name" :value="item.value" :key="item.name">
  <input type="submit" name="submit" value="submit" ref="twPayBtn">
</form>

twPay(){
  //请求后台拿相关属性，并将其处理成twPayObjArr
  //然后提交
  this.$nextTick(()=>{
    this.$refs.twPayBtn.click();
  })
}
```
提交后会自动跳转到对应商店，成功和失败的回调地址在后台控制

## eghl
eghl是马来西亚地区使用的支付方式之一
上码
```
<form name="eghl_pay" method="post" action="这里放测试环境或者正式环境的地址" hidden id="eghl_form"> 
  <input type="hidden" v-for="item in eghlObjArr" :name="item.name" :value="item.value" :key="item.name">
  <input type="submit" name="submit" value="submit" ref="eghlPayBtn">
</form>

eghlPay(){
  //请求后台拿相关属性，并将其处理成eghlObjArr
  //然后提交
  this.$nextTick(()=>{
    this.$refs.eghlPayBtn.click();
  })
}
```
提交后会自动跳转到对应商店，成功和失败的回调地址在后台控制
注意：eghl支付有对域名进行白名单限制，只有在白名单的域名下才能发起支付

## vue支付总结
这几种支付都各有特点，特此记录！
支付有风险，谨防诈骗！！！