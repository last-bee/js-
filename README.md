## js的常见操作的总结
### 判断class是否存在**返回true OR false**
``` js

export function hasClass(el, className) {
  let reg = new RegExp('(^|\\s)' + className + '(\\s|$)')
  return reg.test(el.className)
}
```
### 添加class **依赖hasClass**
```
  export function addClass(el, className) {
    if (hasClass(el, className)) {
      return
    }

    let newClass = el.className.split(' ')
    newClass.push(className)
    el.className = newClass.join(' ')
  }
```
### 给transform添加前缀
```
 let elementStyle = document.createElement('div').style//创建div的属性

 let vendor = (() => {
    let transformNames = {//浏览器的前缀名
      webkit: 'webkitTransform',
      Moz: 'MozTransform',
      O: 'OTransform',
      ms: 'msTransform',
      standard: 'transform'
    }

    for (let key in transformNames) {//判断哪个供应商
      if (elementStyle[transformNames[key]] !== undefined) {
        return key
      }
    }

    return false//全部不支持有问题
 })()

 export function prefixStyle(style) {
    if (vendor === false) {//供应商
      return false//不存在
    }

    if (vendor === 'standard') {//标准
      return style
    }

    return vendor + style.charAt(0).toUpperCase() + style.substr(1)//首字母大写
 }
```
### 对象参数变成路径参数 
* url += (url.indexOf('?') < 0 ? '?' : '&') + param(data) 
#### **如果url有？则直接添加&**</br>
#### **如果url无？则添加？**
```
 export function param(data) {
  let url = ''
  for (var k in data) {
    let value = data[k] !== undefined ? data[k] : ''
    url += '&' + k + '=' + encodeURIComponent(value)
  }
  return url ? url.substring(1) : ''
}
```
### 封装 jsonp
* 解决跨域的一种方式 [node-jsonp链接](https://www.npmjs.com/package/node-jsonp)
#### 原理  
* script不存在跨域
* 通过请求script的URL
* 匿名函数执行的回调

```
import originJsonp from 'jsonp'
export default function jsonp(url, data, option) {
  url += (url.indexOf('?') < 0 ? '?' : '&') + param(data)
  return new Promise((resolve, reject) => {
    originJsonp(url, option, (err, data) => {
      if (!err) {
        resolve(data)
      } else {
        reject(err)
      }
    })
  })
```
}
