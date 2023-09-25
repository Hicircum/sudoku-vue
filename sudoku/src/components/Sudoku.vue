<template>
    <div class="game-warp">
      <div class="game-board">
        <div class="game-inner">
          <table class="game-table">
            <tbody>
              <tr v-for="(i, row) in problem" class="game-row">
                <td
                v-for="(x, col) in i"
                class="game-col"
                >
                    <SudokuCell
                    :cellNumber="x"
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

defineProps({
    problem: Array
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
</style>