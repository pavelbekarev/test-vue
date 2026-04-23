<script setup lang="ts">
import { computed, ref } from "vue";

const startX = ref<number | null>();
const startY = ref<number | null>();
const endX = ref<number | null>()
const endY = ref<number | null>()

const loadedImage = ref<any>();

const selection = ref();

const isSelection = ref<boolean>(false);

const mouseDownEvent = (e: MouseEvent) => {
    isSelection.value = true;
    startX.value = e.offsetX;
    startY.value = e.offsetY

    selection.value.style.left = startX.value.toString() + 'px';
    selection.value.style.top = startY.value.toString() + 'px';
    selection.value.style.width = '0px';
    selection.value.style.height = '0px';
    selection.value.style.display = 'block'
}

const mouseMoveEvent = (e: MouseEvent) => {
    if (!isSelection.value) return;

    endX.value = e.offsetX;
    endY.value = e.offsetY;

    selection.value.style.width = Math.abs(width.value) + 'px';
    selection.value.style.height = Math.abs(height.value) + 'px';

    selection.value.style.left = (width.value < 0 ? endX.value : startX.value) + "px";
    selection.value.style.top = (height.value < 0 ? endY.value : startY.value) + "px";
}

const mouseUpEvent = (e: MouseEvent) => {
    isSelection.value = false
}

const width = computed(() => {
    return endX.value - startX.value
})

const height = computed(() => {
    return endY.value - startY.value
})

const loadImage = (e: Event) => {
    if (!e.target.files || e.target.files.length === 0) return;

    const reader = new FileReader();
    reader.onload = () => {
        loadedImage.value = reader.result;
    }

    reader.readAsDataURL(e.target.files[0])
}



const countCoords = () => {
    const resultStartX = computed(() => {
        return startX.value;
    })

    const resultStartY = computed(() => {
        return startY.value;
    })

    const resultEndX = computed(() => {
        return startX.value + width.value;
    })

    const resultEndY = computed(() => {
        return startX.value + height.value;
    })

    return {
        resultStartX, resultStartY, resultEndX, resultEndY
    }
}

const {resultEndX, resultEndY, resultStartX, resultStartY} = countCoords();
</script>

<template>
    <input type="file" @change="loadImage">
    
    <div ref="targetArea" class="targetArea" @mousedown="mouseDownEvent" @mousemove="mouseMoveEvent" @mouseup="mouseUpEvent">
        <img class="img" :src="loadedImage" alt="image">
        <div ref="selection" class="selection" v-if="isSelection"></div>
    </div>

    <span class="result">{{ resultStartX }}</span>
    <span class="result">{{ resultStartY }}</span>
    <span class="result">{{ resultEndX }}</span>
    <span class="result">{{ resultEndY }}</span>
</template>

<style scoped>
.targetArea {
    position: relative;
    width: 700px;
    height: 700px;
}

.selection {
    position: absolute;
    border: 2px dashed blue;
    background: rgba(0, 0, 255, 0.2);
    pointer-events: none;
}
.img {
    pointer-events: none;
    width: 100%;
    height: 100%;
    object-fit: cover;

}

.result {
    display: flex;
    flex-flow: column;
    position: relative;
    right: 0;
    bottom: 10%;
}

.result:nth-child(1n) {
    margin-top: 25px;
}
</style>
