---
title: '楼小三闸机二维码'
date: 2021-03-09 14:26:33
tags: '微信小程序工具'
top_img: 'linear-gradient(to right, #eea2a2 0%, #bbc1bf 19%, #57c6e1 42%, #b49fda 79%, #7ac5d8 100%)'
cover: false
copyright_url: https://hase.org.cn/2021/03/04/mybo4/
copyright_info: 转载请注明来自 Hase！
---

## 楼小三
楼小三是由楼小二有感而写的小程序工具，小程序搜索"louxiaosan"即可体验
我先解释一下什么是楼小二：商业写字楼企业服务平台,为楼内企业提供一站式办公服务,提升写字楼办公效率,降低办公成本,升级办公体验的APP。其实就是写字楼进电梯前有个闸机，需要打开这个APP里的身份二维码让闸机扫码才能进入，一般是写字楼为了防止陌生人随意进入大厦，而设置的一个工具。
那么要进入大厦就得下载他的APP，打开APP要关闭一些广告弹窗，还要点开几个层级的界面才能出现二维码。如果遇到上班高峰或者午餐高峰，网络不好的情况，那就只能跟别人屁股进入了。而且提前截好二维码的图片过了一段时间又不能用。这哪里有提升效率呢？
带着疑问用解码工具扫了他的二维码，发现二维码里唯一会变的数据竟然只是一串时间戳。。。
那我写个小程序，加个定时器定时改变时间戳，生成个二维码是否可以通过呢。哈哈哈。
上码
```
//设置页
//set.js
Page({
  sao(){
    //移除本地保存的数据从新扫码
    wx.removeStorage({
      key: 'data',
      success: function (res) {
        console.log(res)
      }
    })
    //调用小程序内置的扫码api（scanCode）
    wx.scanCode({
      success: (res) => {
        //将扫到的二维码的内容保存在本地
        wx.setStorage({
          key:'data',
          data: res.result
        });
        //跳转到二维码初始页面
        wx.redirectTo({
          url: '../index/index'
        })
      }
    })
  }
})

//set.wxml
<view>
  <button bindtap="sao" class="btn">扫一扫</button>
  <view class="txt">
    请先扫一扫楼小二
  </view>
</view>

//初始页
//index.wxml
<view class="container">
  <canvas style="width: 200px; height: 200px;" canvas-id="myQrcode"></canvas>
  <button bindtap="changeText" class="btn">刷新</button>
  <button bindtap="changeSet" class="btn2">设置</button>
</view>

//index.js
//引入小程序二维码生成的插件
import drawQrcode from '../../utils/weapp.qrcode.js'
Page({
  data: {
    //二维码需要的数据如下，字段除了电话和时间戳，其他是猜的
    userId: '',//
    userPhone: '',
    userDate: '',
    uniId: '',
  },
  onLoad() {
    //拿本地数据
    wx.getStorage({
      key:'data',
      success:(res)=>{
        let myData = res.data.split("|");
        this.setData({
          userId: myData[0],
          userPhone: myData[1],
          uniId: myData[3],
        });
        //执行时间戳获取方法
        this.changeText();
      },
      fail:()=>{
        //打开小程序如果没有数据会自动跳转设置页
        wx.redirectTo({
          url: '../set/set'
        })
      },
    });
    let jishi = setInterval(() => {
      this.changeText();
    }, 60000);
  },


  //画二维码的方法
  draw() {
    drawQrcode({
      width: 200,
      height: 200,
      canvasId: 'myQrcode',
      typeNumber: -1,
      correctLevel: 0,
      text: this.data.userId + '|' + this.data.userPhone + '|' + this.data.userDate + '|' + this.data.uniId,
      callback(e) {
        console.log('e: ', e)
      }
    })
  },
  //获取最新的时间戳
  changeText() {
    let myDate = Date.parse(new Date());
    this.data.userDate = myDate;
    this.draw()
  },
  //手动跳转设置界面
  changeSet(){
    wx.redirectTo({
      url: '../set/set'
    })
  },
})
```
这代码的功能就两个，扫码保存信息在本地，用本地信息生成新的二维码。
由于信息都是本地的，所以根本不担心网络问题，也不用看到心烦的广告了。
小三上线两个月后，闸机竟然换成了人脸识别系统的了，猝！
