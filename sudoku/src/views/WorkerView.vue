<template>
  <div class="conatiner">
    <h1>Worker BenchMark</h1>
    <span>
      <el-button type="primary" @click="concurrency">并发生成50个</el-button>
      <el-button type="primary" @click="serial">串行生成50个</el-button>
    </span>
    <span><span>并发时间</span>{{ workerTime }}<span>ms</span></span>
    <span><span>串行时间</span>{{ serialTime }}<span>ms</span></span>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const workerTime = ref(0)
const serialTime = ref(0)

const concurrency = () => {
  const workers = []
  const results = []
  // 统计运行时间
  const start = new Date().getTime()
  for (let i = 0; i < 50; i++) {
    const worker = new Worker(new URL('@/worker/test.js', import.meta.url))
    worker.postMessage({"difficulty": "easy", "debug": false})
    worker.onmessage = (e) => {
      results.push(e.data)
      if (results.length === 50) {
        console.log(results)
        const end = new Date().getTime()
        workerTime.value = end - start
      }
    }
    workers.push(worker)
  }
}

const serial = () => {
  const start = new Date().getTime()
  const worker = new Worker(new URL('@/worker/test.js', import.meta.url))
  worker.postMessage({"difficulty": "easy", "debug": true})
  worker.onmessage = (e) => {
    const end = new Date().getTime()
    serialTime.value = end - start
    console.log(e.data)
  }
}
</script>

<style lang="less" scoped>
.conatiner{
  display: flex;
  flex-direction: column;
  align-items: center;
  span{
    margin: 10px;
  }
}
</style>
