---
title: 'vue的支付业务（一）：微信支付'
date: 2021-03-04
tags: 'vue支付'
top_img: 'linear-gradient(to right, #eea2a2 0%, #bbc1bf 19%, #57c6e1 42%, #b49fda 79%, #7ac5d8 100%)'
cover: false
---

这里记录了使用vue技术栈，包括微信、微信小程序、支付宝、stripe、蓝新、eghl等支付业务的代码

## vue微信支付

微信支付有两种方式，一种是生成二维码用微信扫码支付（适合PC端），一种是在微信里调起支付（适合M端）。
相关文档[jsapi支付](https://pay.weixin.qq.com/wiki/doc/api/jsapi_sl.php?chapter=7_7&index=6)、[H5支付](https://pay.weixin.qq.com/wiki/doc/api/H5_sl.php?chapter=15_4)

### 二维码支付

二维码支付逻辑比较简单，给后台提交相关参数（订单ID等），后台会返回生成二维码的字符串，使用[vue-qr](https://www.npmjs.com/package/vue-qr)将字符串生成二维码即可。
如支付成功，商户后台会接收到微信侧的异步通知；
用户在微信支付收银台完成支付或取消支付,返回商户页面（默认为返回支付发起页面）；
商户在展示页面，引导用户主动发起支付结果的查询；
商户后台判断是否接到收微信侧的支付结果通知，如没有，后台调用我们的订单查询接口确认订单状态；
展示最终的订单支付结果给用户。

### 微信里调起支付
注意：WeixinJSBridge内置对象在其他浏览器中无效。
上码！！
```
//首先判断是否微信内部打开，否则return
let ua = window.navigator.userAgent.toLowerCase();
if (ua.indexOf('micromessenger') > -1) {
  //内部打开，进行下一步操作
  //获取appid
  //用getCodeApi方法获取带有code的url，用getUrlKey方法获取url中的code(this.getUrlKey("code");)
} else {
  //非微信内部
  return;
}

//静默授权，用户无感知，用于获取支付要用的code
getCodeApi(state){
  let urlNow=location.href; //当前的url
  let scope='snsapi_base';    //snsapi_userinfo   //静默授权 用户无感知
  let appid=this.appid; //需要后台返回
  let url=`https://open.weixin.qq.com/connect/oauth2/authorize?appid=${appid}&redirect_uri=${urlNow}&response_type=code&scope=${scope}&state=${state}&&connect_redirect=1#wechat_redirect`;
  console.log(url);
  window.location.replace(url);
},
getUrlKey(name){//获取url 参数code
  return decodeURIComponent((new RegExp('[?|&]'+name+'='+'([^&;]+?)(&|#|;|$)').exec(location.href)||[,""])[1].replace(/\+/g,'%20'))||null;
},
```

判断用户发起支付后
```
//同样要判断是否微信打开
this.wxData = res.data.data;//这个是后台返回的，微信支付所需要的参数
WeixinJSBridge.invoke( 'getBrandWCPayRequest', {
  "appId":this.wxData.appId,     //公众号名称,由商户传入     
  "timeStamp":this.wxData.timeStamp,         //时间戳,自1970年以来的秒数     
  "nonceStr":this.wxData.nonceStr, //随机串     
  "package":this.wxData.package,     
  "signType":this.wxData.signType,         //微信签名方式：     
  "paySign":this.wxData.paySign //微信签名 
  }, 
  function(res){//支付回调
    console.log(res);  
    if(res.err_msg == "get_brand_wcpay_request:ok" ) {
      console.log('支付成功');
      this.$router.replace('/')
      //支付成功后跳转的页面
    }else if(res.err_msg == "get_brand_wcpay_request:cancel"){
      console.log('支付取消');
    }else if(res.err_msg == "get_brand_wcpay_request:fail"){
      console.log('支付失败');
      WeixinJSBridge.call('closeWindow');
    } //使用以上方式判断前端返回,微信团队郑重提示：res.err_msg将在用户支付成功后返回ok,但并不保证它绝对可靠。
  }
);
```

### 微信小程序支付
上码！！
```
//获取支付要的参数
const data = JSON.parse(res.data.data);
/// 调起支付
wx.requestPayment({
  nonceStr: data["nonceStr"],
  package: data["package"],
  paySign: data["paySign"],
  timeStamp: data["timeStamp"],
  signType:data["signType"],
  appId:data["appId"],
  //支付回调
  //可以在回调里面发起请求告诉后台支付结果
  //后台返回后可以调整对应界面，或者跳回APP
  success:res=>{
    console.log(res)
  },
  fail:res=>{
   console.log(res)
  }
})

//wxml里面返回app的按钮
<button type="primary" open-type="launchApp" app-parameter="{{parm}}">返回APP</button>
//launchAPP是返回app必要参数
//parm是与APP约定的返回参数
```

有问题请直接加我QQ（邮箱）探讨。