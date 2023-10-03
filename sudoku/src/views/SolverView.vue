<template>
  <Sudoku :problem="problem" :is-sovler="true" />
</template>

<script setup>
import { ref } from 'vue';
import Sudoku from '../components/Sudoku.vue';
const problem = ref([
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
</script>

<style lang="less" scoped>

</style>