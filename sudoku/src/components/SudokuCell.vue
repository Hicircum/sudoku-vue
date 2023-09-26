<template>
    <div class="cell">
        <div
        class="cell-value"
        :class="{
            'selected': isSelected,
            'hover': isHover,
            'same': isSame,
            'problem': isProblem,
            'user-input': !isProblem
        }"
        >
            <div class="cell-value-inner">
                {{ handleZero(cellNumber) }}
            </div>
        </div>
    </div>
</template>

<script setup>
defineProps({
    cellNumber: Number,
    isHover: Boolean,
    isSelected: Boolean,
    isSame: Boolean,
    isProblem: Boolean,
})

function handleZero(num) {
    if (num === 0) {
        return ''
    }else{
        return num
    }
}
</script>

<style lang="less" scoped>
.cell {
    width: 100%;
    height: 100%;
    .err::after{
        position: absolute;
        content: "";
        right: 8%;
        bottom: 8%;
        width: 14%;
        height: 14%;
        background-color: #ff0000;
        border-radius: 50%;
    }
    .problem{
        background-color: #dadada;
    }
    .selected{
        background-color: #c3fc7e;
    }
    .hover{
        background-color: #caff8a !important;
    }
    .same{
        background-color: #f2fde5;
    }
    .user-input{
        color: #5400c9 !important;
    }
    .cell-value {
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
        font-weight: 100;
        text-align: center;
        .cell-value-inner {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            font-size: calc(20px + 1vw);
            font-weight: bold;
            font-family: system-ui, -apple-system, BlinkMacSystemFont,
                        'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, 'Open Sans',
                        'Helvetica Neue', sans-serif;
        }
    }
}
</style>