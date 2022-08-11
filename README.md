# 异步延时加载库文件



以tinymce库为例：

1. 封装一个`loadTinyMce` 库加载函数，函数内通过`loaded`标志组件是否加载完成，并返回一个`Promise`。
2. 使用`setTimeout`指定需要延迟加载库的时间，延时结束后通过返回的`Promise`来完成`tinymce`初始化。
3. 可以将`loadTinyMce`导出，方便项目中其他地方复用。

```javascript
const loadTinyMce = (function () {
  let loadedCb = []
  let loaded = false
  return function () {
    return new Promise((resolve, reject) => {
      // 加载过一次下次不用再加载，方便其他地方复用
      if (loaded) return resolve(window.tinymce)
      if (loadedCb.length === 0) {
        const script = document.createElement('script')
        script.src = 'https://cdnjs.cloudflare.com/ajax/libs/tinymce/6.1.2/tinymce.min.js';
        script.onload = function () {
          loaded = true
          loadedCb.forEach(v => {
            v()
          })
        }
        document.body.appendChild(script)
      }
      loadedCb.push(() => {
        resolve(window.tinymce)
      })
    })
  }
}())

// 等1s再加载
setTimeout(() => {
  loadTinyMce().then((tinymce) => {
    tinymce.init({
        selector: '#tinymce-container', //容器，可使用css选择器
    });
  })
}, 1000)
```

