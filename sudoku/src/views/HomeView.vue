<template>
  <ToolBar @change-diff="changeDifficulty" @next-prob="nextProblem" :difficulty="difficulty"/>
  <Sudoku :problem="problem"/>
</template>

<script setup>
import { ref, onMounted, onUpdated } from 'vue';
import ToolBar from '../components/ToolBar.vue';
import Sudoku from '../components/Sudoku.vue';


const problem = ref([])
const easyProblem = ref([])


const difficulty = ref('easy')
const difficultyList = [
    "easy",
    "hard",
    "very hard",
    "insane"
]
const changeDifficulty = () => {
    const index = difficultyList.indexOf(difficulty.value)
    if (index === difficultyList.length - 1) {
        difficulty.value = difficultyList[0]
    } else {
        difficulty.value = difficultyList[index + 1]
    }
}

function genProblem() {
  const workers = []
  const results = []
  for (let i = 0; i < 10; i++) {
    const worker = new Worker(new URL('@/worker/sudoku.js', import.meta.url))
    worker.postMessage({"difficulty": difficulty.value, "debug": false})
    worker.onmessage = (e) => {
      results.push(e.data)
      if (results.length === 10) {
        problem.value = results.pop()
        console.log("gen new problem " + difficulty.value)
        if(difficulty.value === 'easy'){
          easyProblem.value = results
        }
      }
    }
    workers.push(worker)
  }
}

function nextProblem() {
  if(difficulty.value === 'easy'){
    if(easyProblem.value.length === 0){
      genProblem()
    }else{
      problem.value = easyProblem.value.pop()
      console.log(easyProblem.value.length)
    }
  }
}

onMounted(() => {
  nextProblem()
})
</script>

<style>
body{
  margin: 0;
}
</style>