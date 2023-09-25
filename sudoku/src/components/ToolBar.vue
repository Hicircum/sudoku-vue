<template>
    <div class="nav">
        <div class="logo">
            <img src="@/assets/logo.svg" alt="logo" />
        </div>
        <div class="item">
            <span>
                <el-button plain @click="changeDifficulty">{{ difficulty }}</el-button>
            </span>
        </div>
        <div class="item">
            <el-button type="primary">重开</el-button>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue'

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
</script>

<style lang="less" scoped>
.nav{
    display: flex;
    height: 58px;
    border-bottom: .8px solid #cfcfcf;
    align-items: center;
    justify-content: space-between;
    .logo{
        display: flex;
        align-items: center;
        padding-left: 20px;
        padding-right: 20px;
        img{
            width: 40px;
            height: 40px;
        }
    }
    .item{
        display: flex;
        align-items: center;
        padding-left: 20px;
        padding-right: 20px;
        span{
            font-size: 20px;
            padding-left: 10px;
            padding-right: 10px;
        }
    }
}
</style>