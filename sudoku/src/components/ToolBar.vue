<template>
    <div class="nav">
        <div class="logo">
            <img src="@/assets/logo.svg" alt="logo" />
        </div>
        <div class="item">
            <span>
                <el-button plain @click="$emit('changeDiff')">{{ difficulty }}</el-button>
            </span>
        </div>
        <div class="item">
            <el-button type="primary" @click="$emit('nextProb')">重开</el-button>
            <el-button type="primary" @click="$emit('autoSolve')">题解</el-button>
            <RouterLink to="/solver">
                <el-button type="primary">求解器</el-button>
            </RouterLink>
        </div>
    </div>
</template>

<script setup>
import { RouterLink } from 'vue-router';
defineProps({
    difficulty: String
})
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
        a{
            padding-left: 10px;
        }
    }
}
</style>