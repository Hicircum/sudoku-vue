<template>
  <div class="actions">
    <div class="btn">
      <el-button size="large">Large</el-button>
    </div>
    <div class="btn">
      <el-button size="large">Large</el-button>
    </div>
    <div class="btn">
      <el-button size="large">Large</el-button>
    </div>
  </div>
</template>

<style lang="less" scoped>
.actions {
  display: flex;
  justify-content: center;
  flex-direction: column;
  height: 100%;
  .btn{
    text-align: center;
    margin-bottom: 20px;
  }
}
</style>