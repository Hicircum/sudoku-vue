<template>
  <ToolBar
  @change-diff="changeDifficulty"
  @next-prob="genProblem"
  @auto-solve="solveProblem"
  :difficulty="difficulty"
  />
  <div class="sudoku-conatiner">
    <div class="selector">
      <el-radio-group v-model="problemIndex">
        <el-radio-button
        v-for="item in problemList.length"
        :label="item" />
      </el-radio-group>
    </div>
    <Sudoku :problem="problem"/>
  </div>
</template>

<script setup>
import { ref, onMounted, watch } from 'vue';
import ToolBar from '../components/ToolBar.vue';
import Sudoku from '../components/Sudoku.vue';


const problem = ref([])
// const easyProblem = ref([])
// const mediumProblem = ref([])
// const hardProblem = ref([])
// const veryHardProblem = ref([])
const problemList = ref([])
const problemIndex = ref(0)


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
    worker.postMessage({"difficulty": difficulty.value, "solve": false})
    worker.onmessage = (e) => {
      results.push(e.data)
      if (results.length === 10) {
        // problem.value = results.pop()
        // console.log("gen new problem " + difficulty.value)
        // if(difficulty.value === 'easy'){
        //   easyProblem.value = results
        // }
        // if(difficulty.value === 'medium'){
        //   mediumProblem.value = results
        // }
        // if(difficulty.value === 'hard'){
        //   hardProblem.value = results
        // }
        // if(difficulty.value === 'very-hard'){
        //   veryHardProblem.value = results
        // }
        problemList.value = results;
        problemIndex.value = 1;
        problem.value = problemList.value[0]
      }
    }
    workers.push(worker)
  }
}

function solveProblem() {
  const workers = []
  const results = []
  for (let i = 0; i < 10; i++) {
    const worker = new Worker(new URL('@/worker/sudoku.js', import.meta.url))
    let boardStr = "";
    for(let j = 0; j < 9; j++){
      for(let k = 0; k < 9; k++){
        if(problemList.value[i][j][k] === 0){
          boardStr += "."
        }else{
          boardStr += problemList.value[i][j][k].toString();
        }
      }
    }
    worker.postMessage({"solve": true, "problem": boardStr, "index": i})
    worker.onmessage = (e) => {
      results.push(e.data);
      if (results.length === 10) {
        const temp = []
        for(let i = 0; i < 10; i++){
          for(let j = 0; j < 10; j++){
            if(results[j][0] === i){
              temp.push(results[j][1])
            }
          }
        }
        problemList.value = temp;
        problemIndex.value = 1;
        problem.value = problemList.value[0]
      }
    }
    workers.push(worker)
  }
}

// function nextProblem() {
//   if(difficulty.value === 'easy'){
//     if(easyProblem.value.length === 0){
//       genProblem()
//     }else{
//       problem.value = easyProblem.value.pop()
//       console.log(easyProblem.value.length)
//     }
//   }
//   if(difficulty.value === 'medium'){
//     if(mediumProblem.value.length === 0){
//       genProblem()
//     }else{
//       problem.value = mediumProblem.value.pop()
//       console.log(mediumProblem.value.length)
//     }
//   }
//   if(difficulty.value === 'hard'){
//     if(hardProblem.value.length === 0){
//       genProblem()
//     }else{
//       problem.value = hardProblem.value.pop()
//       console.log(hardProblem.value.length)
//     }
//   }
//   if(difficulty.value === 'very-hard'){
//     if(veryHardProblem.value.length === 0){
//       genProblem()
//     }else{
//       problem.value = veryHardProblem.value.pop()
//       console.log(veryHardProblem.value.length)
//     }
//   }
// }

onMounted(() => {
  // nextProblem()
  genProblem()
})

watch(problemIndex, () => {
  problem.value = problemList.value[problemIndex.value - 1]
})
</script>

<style lang="less">
body{
  margin: 0;
}
.sudoku-conatiner{
  margin-top: 10px;
  .selector{
    margin-bottom: 10px;
    text-align: center;
  }
}
</style>