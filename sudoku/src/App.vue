<script setup>
import { ref } from 'vue'
import { RouterLink, RouterView } from 'vue-router'

const sendData = () => {
  const workers = []
  const results = []
  // 统计运行时间
  const start = new Date().getTime()
  for (let i = 0; i < 10; i++) {
    const worker = new Worker(new URL('@/worker/test.js', import.meta.url))
    worker.postMessage({"difficulty": "easy", "debug": false})
    worker.onmessage = (e) => {
      results.push(e.data)
      if (results.length === 10) {
        console.log(results)
        const end = new Date().getTime()
        console.log(end - start)
      }
    }
    workers.push(worker)
  }
}
</script>

<template>
  <!-- <header>
    <button @click="sendData">test</button>

    <div class="wrapper">

      <nav>
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/worker">About</RouterLink>
      </nav>
    </div>
  </header> -->

  <RouterView />
</template>

