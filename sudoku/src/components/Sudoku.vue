<template>
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
                    :cellNumber="x"
                    :isHover="isHover(row, col)"
                    :isSelected="isSelected(row, col)"
                    :isSame="isSame(row, col)"
                    :isProblem="isProblem(row, col)"
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
import { ref } from 'vue'
import SudokuCell from './SudokuCell.vue'

const props = defineProps({
    problem: Array
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
</style>