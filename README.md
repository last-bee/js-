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
}
```
### 判断终端是否为手机
``` javascript
function isWeixinBrowser() {
    var ua = navigator.userAgent;
    var ipad = ua.match(/(iPad).*OS\s([\d_]+)/)
    var isIphone = !ipad && ua.match(/(iPhone\sOS)\s([\d_]+)/)
    var isAndroid = ua.match(/(Android)\s+([\d.]+)/)
    var isMobile = isIphone || isAndroid;
    var agent = navigator.userAgent.toLowerCase();
    if (agent.match(/MicroMessenger/i) == "micromessenger"&&isMobile) {
        return true;
    } else {
        return false;
    }
}
```
### js中的遍历
#### var arr = [1,2,3,4,5,6]
* for循环，需要知道数组的长度，才能遍历
* forEach ***循环数组中每一个元素并采取操作， 没有返回值， 可以不用知道数组长度***
``` javascript
 arr.forEach((item,index)=>{
    item = item + 'hello'
    console.log(item)
  })
  console.log(arr)
  //1hello
  //2hello
  //3hello
  //4hello
  //5hello
  //6hello
  //[1,2,3,4,5,6]
```
  
*  map函数，遍历数组每个元素，并回调操作，需要返回值，返回值组成新的数组，原数组不变
``` javascript
var newArr = arr.map((item,index)=>{
    item = item + 'hello'
	  return (item)
})
console.log(newArr)//["1hello", "2hello", "3hello", "4hello", "5hello", "6hello"]
console.log(arr)//[1,2,3,4,5,6]
```
* filter函数， 过滤通过条件的元素组成一个新数组， 原数组不变
``` javascript
  var newArr = arr.filter((item)=>{
    if(item>2){
      return item
    }
  })
  console.log(newArr)//[3,4,5,6]
  console.log(arr)//[1,2,3,4,5,6]
```
* some函数，遍历数组中是否有符合条件的元素，返回Boolean值
``` javascript 
//判断是否可以被开平方
var newArr = arr.some((item)=>{
   return item>1&&!~Math.sqrt(item).toString().indexOf('.')
})  
console.log(newArr)//true
console.log(arr)//[1,2,3,4,5,6]
```
* every函数， 遍历数组中是否每个元素都符合条件， 返回Boolean值
``` javascript
var newArr = arr.every((item)=>{
   return item>1&&!~Math.sqrt(item).toString().indexOf('.')
})  
console.log(newArr)//false
console.log(arr)//[1,2,3,4,5,6]
```
### sort排序
* 数组排序
``` javascript
var sortArr = [1,2,3,5,6,7,9,0,-12]
var newArr =  sortArr.sort((a,b)=>{//a-b 从小到大排序 b-a 从大到小
    return a-b //大于0或者为true 交换位置
})
newArr // [-12, 0, 1, 2, 3, 5, 6, 7, 9]
```
* 数组对象排序
``` javascript
var sortArr = [{num:10},{num:2},{num:3},{num:5},{num:6},{num:8}]
var newArr = sortArr.sort((a,b)=>{
   return a.num > b.num
})
console.log(newArr)//[{num:2}, {num:3}, {num:5}, {num:6}, {num:8}, {num:10}]
```
### axios
* axios请求拦截
``` javascript
axios.interceptors.request.use((config)=>{
    //发起请求前的业务处理
    return config
},(error)=>{
    return Promise.reject(error)
})
```
* axios响应拦截
``` javascript
axios.interceptors.response.use((response)=>{
    //对相应数据做处理
    return response
},(error)=>{
    return Promise.reject(error)
})
```
[axios链接](https://github.com/axios/axios/issues/164#issuecomment-327837467)
