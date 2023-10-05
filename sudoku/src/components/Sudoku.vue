<template>
    <div class="sovle" v-if="isSovler">
        <el-button type="primary" @click="getAns">题解</el-button>
        <RouterLink to="/">
            <el-button type="success">主页</el-button>
        </RouterLink>
    </div>
    <div class="game-warp">
      <div class="game-board">
        <div class="game-inner">
          <table class="game-table">
            <tbody>
              <tr v-for="(i, row) in problem" class="game-row">
                <td
                v-for="(x, col) in i"
                @mouseenter="mouseEnter(row, col)"
                @mouseleave="mouseLeave"
                @click="clickCell(row, col)"
                class="game-col"
                >
                    <SudokuCell
                    :cellNumber="handleDigit(x, row, col)"
                    :isHover="isHover(row, col)"
                    :isSelected="isSelected(row, col)"
                    :isSame="isSame(row, col)"
                    :isProblem="isProblem(row, col)"
                    :isErr="!isValid(row, col)"
                    />
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
</template>

<script setup>
import { ref, watch } from 'vue'
import { RouterLink } from 'vue-router'
import SudokuCell from './SudokuCell.vue'

const props = defineProps({
    problem: Array,
    isSovler: Boolean
})

const hoverCell = ref({
    row: -1,
    col: -1
})

const selectedCell = ref({
    row: -1,
    col: -1
})

const userAnswer = ref([
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0,0,0],
    [0,0,0,0,0,0,0,0,0]
])

// 鼠标事件处理
function mouseEnter(row, col) {
    hoverCell.value.row = row
    hoverCell.value.col = col
}

function mouseLeave() {
    hoverCell.value.row = -1
    hoverCell.value.col = -1
}

function clickCell(row, col) {
    if(selectedCell.value.row === row && selectedCell.value.col === col){
        selectedCell.value.row = -1
        selectedCell.value.col = -1
    }else{
        selectedCell.value.row = row
        selectedCell.value.col = col
    }
}

// 单元格样式处理
function isHover(row, col) {
    return hoverCell.value.row === row && hoverCell.value.col === col
}

function isSelected(row, col) {
    return selectedCell.value.row === row && selectedCell.value.col === col
}

function isSame(row, col) {
    if(selectedCell.value.row == row && selectedCell.value.col == col){
        return false
    }
    if(selectedCell.value.row == row || selectedCell.value.col == col){
        return true
    }
    if(Math.floor(selectedCell.value.row / 3) == Math.floor(row / 3) 
        && Math.floor(selectedCell.value.col / 3) == Math.floor(col / 3)){
        return true
    }
}

function isProblem(row, col) {
    return props.problem[row][col] !== 0
}

function handleDigit(x, row, col) {
    if(x === 0){
        return userAnswer.value[row][col]
    }
    return props.problem[row][col]
}

// 求解用户输入
function getAns(){
    if(!props.isSovler){
        return
    }
    const worker = new Worker(new URL('@/worker/sudoku.js', import.meta.url))
    let boardStr = "";
    for(let j = 0; j < 9; j++){
        for(let k = 0; k < 9; k++){
            if(userAnswer.value[j][k] === 0){
                boardStr += "."
            }else{
                boardStr += userAnswer.value[j][k].toString();
            }
        }
    }
    worker.postMessage({"solve": true, "problem": boardStr})
    worker.onmessage = (e) => {
        const ans = e.data
        console.log(ans)
        for(let i = 0; i < 9; i++){
            for(let j = 0; j < 9; j++){
                userAnswer.value[i][j] = ans[1][i][j]
            }
        }
    }
    worker.onerror = (e) => {
        ElNotification({
            title: 'Error',
            message: e.message,
            type: 'error',
        })
    }
}


function isValid(row, col) {
    // 检查是否有重复数字，有则返回false
    if(getNum(row, col) === 0){
        return true
    }
    for(let i = 0; i < 9; i++){
        if(i !== col && getNum(row, i) === getNum(row, col)){
            return false
        }
        if(i !== row && getNum(i, col) === getNum(row, col)){
            return false
        }
    }
    for(let i = Math.floor(row / 3) * 3; i < Math.floor(row / 3) * 3 + 3; i++){
        for(let j = Math.floor(col / 3) * 3; j < Math.floor(col / 3) * 3 + 3; j++){
            if(i !== row && j !== col && getNum(i, j) === getNum(row, col)){
                return false
            }
        }
    }
    return true
}

function getNum(row, col) {
    if(props.problem[row][col] !== 0){
        return props.problem[row][col]
    }
    return userAnswer.value[row][col]
}

// 键盘事件处理
window.addEventListener('keydown', function(e){
  if(e.key == "ArrowUp"){
    if(selectedCell.value.row == 0){
      return
    }
    selectedCell.value.row -= 1
  }
  if(e.key == "ArrowDown"){
    if(selectedCell.value.row == 8){
      return
    }
    selectedCell.value.row += 1
  }
  if(e.key == "ArrowLeft"){
    if(selectedCell.value.col == 0){
      return
    }
    selectedCell.value.col -= 1
  }
  if(e.key == "ArrowRight"){
    if(selectedCell.value.col == 8){
      return
    }
    selectedCell.value.col += 1
  }
  if(e.key == "Escape"){
    selectedCell.value.row = -1
    selectedCell.value.col = -1
  }
})

window.addEventListener('keydown', (e) => {
    if(selectedCell.value.row === -1 || selectedCell.value.col === -1){
        return
    }
    if(e.key >= 1 && e.key <= 9){
        if(isProblem(selectedCell.value.row, selectedCell.value.col)){
            console.log("problem can't be changed")
            return
        }
        userAnswer.value[selectedCell.value.row][selectedCell.value.col] = parseInt(e.key)
    }
    if(e.key === 'Backspace'){
        userAnswer.value[selectedCell.value.row][selectedCell.value.col] = 0
    }
})

// 在problem变化时，清空userAnswer，恢复selectedCell
watch(() => props.problem, () => {
    for(let i = 0; i < 9; i++){
        for(let j = 0; j < 9; j++){
            userAnswer.value[i][j] = 0
        }
    }
    selectedCell.value.row = -1
    selectedCell.value.col = -1
    console.log("problem changed")
})

// 在userAnswer变化时，判断是否完成
watch(userAnswer.value, () => {
    let flag = true
    for(let i = 0; i < 9; i++){
        for(let j = 0; j < 9; j++){
            if(props.problem[i][j] === 0){
                if(userAnswer.value[i][j] === 0){
                    flag = false
                }
            }
        }
    }
    if(flag){
        console.log("finished")
    }
})
</script>

<style lang="less" scoped>
.game-warp{
  display: flex;
  justify-content: center;
}

.game-board {
    position: relative;
    width: 100%;
    max-width: 500px;
    min-width: 250px;
    background-color: #f3f6fa;
    margin: 5px;

    &::after {
        content: '';
        display: block;
        padding-bottom: 100%;
    }

    .game-inner {
        position: absolute;
        top: 0;
        right: 0;
        bottom: 0;
        left: 0;
    }
}


.game-table {
    width: 100%;
    margin: 0 auto;
    padding: 0;
    background: #ffffff;
    user-select: none;
    border: 2px solid #344861;
  
    &,
    &::after {
        display: block;
        box-sizing: border-box;
        height: 100%;
    }

    &::after {
        content: '';
        position: absolute;
        left: calc(100% / 3);
        width: calc(100% / 3);
        top: 0;
        border-left: 2px solid #344861;
        border-right: 2px solid #344861;
        pointer-events: none;
    }
  
    tbody {
        display: flex;
        flex-direction: column;
        width: 100%;
        height: 100%;
        overflow: hidden;
  
      &::after {
        display: block;
        content: '';
        position: absolute;
        left: 0;
        top: calc(100% / 3);
        height: calc(100% / 3);
        border-top: 2px solid #344861;
        border-bottom: 2px solid #344861;
        pointer-events: none;
      }
    }
}

.game-row,
.game-table tbody:after {
    box-sizing: border-box;
    width: 100%;
}

.game-row {
    height: 11.11111%;
}

.game-col,
.game-row {
    display: flex;
    padding: 0;
    margin: 0;
}

.game-col {
    flex-basis: 11.1111%;
    box-sizing: border-box;
    position: relative;
    border-right: 1px solid #bec6d4;
    border-bottom: 1px solid #bec6d4;
    cursor: pointer;
    transform: translateZ(0);
    color: #080808;
}

.game-cover{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  backdrop-filter: blur(10px);
  background-color: rgba(255, 255, 255, 0.5);
  z-index: 1;
}

.sovle{
  display: flex;
  justify-content: center;
  margin-top: 10px;
  margin-bottom: 10px;
  a{
    margin-left: 10px;
  }
}
</style>