<script setup>
import HelloWorld from './components/HelloWorld.vue'
import TheWelcome from './components/TheWelcome.vue'

// 这里是我要写的
const loadTinyMce = (function () {
  let loadedCb = []
  let loaded = false
  return function () {
    return new Promise((resolve, reject) => {
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


</script>

<template>
  <header>
    <img alt="Vue logo" class="logo" src="./assets/logo.svg" width="125" height="125" />

    <div class="wrapper">
      <HelloWorld msg="You did it!" />
    </div>
  </header>

  <main>
    <TheWelcome />
    <div id="tinymce-container"></div>
  </main>
</template>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
