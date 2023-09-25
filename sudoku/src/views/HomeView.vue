<template>
  <ToolBar @change-diff="changeDifficulty" @next-prob="nextProblem" :difficulty="difficulty"/>
  <div class="sudoku-conatiner">
    <Sudoku :problem="problem"/>
  </div>
</template>

<script setup>
import { ref, onMounted, onUpdated } from 'vue';
import ToolBar from '../components/ToolBar.vue';
import Sudoku from '../components/Sudoku.vue';


const problem = ref([])
const easyProblem = ref([])
const mediumProblem = ref([])
const hardProblem = ref([])
const veryHardProblem = ref([])


const difficulty = ref('easy')
const difficultyList = [
    "easy",
    "medium",
    "hard",
    "very-hard"
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
        if(difficulty.value === 'medium'){
          mediumProblem.value = results
        }
        if(difficulty.value === 'hard'){
          hardProblem.value = results
        }
        if(difficulty.value === 'very-hard'){
          veryHardProblem.value = results
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
  if(difficulty.value === 'medium'){
    if(mediumProblem.value.length === 0){
      genProblem()
    }else{
      problem.value = mediumProblem.value.pop()
      console.log(mediumProblem.value.length)
    }
  }
  if(difficulty.value === 'hard'){
    if(hardProblem.value.length === 0){
      genProblem()
    }else{
      problem.value = hardProblem.value.pop()
      console.log(hardProblem.value.length)
    }
  }
  if(difficulty.value === 'very-hard'){
    if(veryHardProblem.value.length === 0){
      genProblem()
    }else{
      problem.value = veryHardProblem.value.pop()
      console.log(veryHardProblem.value.length)
    }
  }
}

onMounted(() => {
  nextProblem()
})
</script>

<style lang="less">
body{
  margin: 0;
}
.sudoku-conatiner{
  margin-top: 20px;
}
</style>