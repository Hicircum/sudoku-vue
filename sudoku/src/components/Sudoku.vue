<template>
{{ problem }}
</template>

<script setup>
import { ref } from 'vue'

defineProps({
    problem: Array
})
</script>