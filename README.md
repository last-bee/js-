## js的常见操作的总结
# 判断class是否存在
``` js
**返回true OR false
export function hasClass(el, className) {
  let reg = new RegExp('(^|\\s)' + className + '(\\s|$)')
  return reg.test(el.className)
}
```
