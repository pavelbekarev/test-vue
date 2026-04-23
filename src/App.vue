<script setup lang="ts">
import { computed, ref, watch } from "vue";
import img1 from "../public/assets/img1.jpg"

const startX = ref<number | null>();
const startY = ref<number | null>();
const endX = ref<number | null>()
const endY = ref<number | null>()

const loadedImage = ref<any>();

document.addEventListener("mousedown", (e: MouseEvent) => {
    startX.value = e.clientX;
    startY.value = e.clientY
})

document.addEventListener("mouseup", (e: MouseEvent) => {
    endX.value = e.clientX;
    endY.value = e.clientY 
})

const width = computed(() => {
    return endX.value - startX.value
})

const height = computed(() => {
    return endY.value - startY.value
})

function drawArea() {
    var canvas = document.getElementById("targetArea");
    const ctx = canvas.getContext("2d")

    ctx.strokeRect(startX.value, startY.value, width, height)
}

watch([() => width.value, () => height.value], (value) => {
    if (value[0] && value[1]) {
        console.debug(value[0], value[1])
        drawArea();
    }
})

const loadImage = (e) => {
    loadedImage.value = e.target.files[0]['name']
}
</script>

<template>
    <input type="file" @change="loadImage">
    <img class="img" :src="loadedImage" alt="image">

    <canvas v-show="width && height" class="targetArea" id="targetArea" :width="width" :height="height"></canvas>
    <span>{{ width }}</span>
</template>

<style scoped>
*, *::before, *::after {
    box-sizing: border-box;
    padding: 0;
    margin: 0;
}

.targetArea {
    position: absolute;
    background-color: red;
}

.img { 
    pointer-events: none;
    width: 100px;
    height: 100px;
}

.imageWrapper {
    pointer-events: none;
    background-image: url();
}
</style>
