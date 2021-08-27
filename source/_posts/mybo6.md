---
title: 'npm发包初体验'
date: 2021-08-27
tags: '发布npm包'
top_img: 'linear-gradient(to right, #eea2a2 0%, #bbc1bf 19%, #57c6e1 42%, #b49fda 79%, #7ac5d8 100%)'
cover: false
copyright_url: https://hase.org.cn/2021/08/27/mybo6/
copyright_info: 转载请注明来自 Hase！
---

## 构建自己的NPM包
日常组件或者方法，封装成自己的NPM包，并发布到npm，使用的时候npm install即可
上码（demo）
```
import myBtn from './myBtn/index.js'//引入自己写好的组件或者方法

const components = [myBtn]

//构建的api，遍历所有组件和方法
const install = function (Vue, opt = {}) {
  if (install.installed) return

  components.map((component) => {
    Vue.component(component.name, component)
  })

  console.log(opt)
}

if (typeof window !== 'undefined' && window.Vue) {
  install(window.Vue)
}

export default install
export { install, myBtn }
```

