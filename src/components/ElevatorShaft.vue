<template>
  <div class="elevatorShaft" :style="elevatorShaftStyle"></div>
</template>
<script setup>
import { computed } from 'vue'
const props = defineProps(['elevatorFloor'])
import { elevatorFloors } from '../config.js'

const elevatorFloorRowHeight = 100
const topFloor = elevatorFloors

const elevatorShaftStyle = computed(() => {
  const transitionDuration =
    props.elevatorFloor < topFloor ? '1s' : `${props.elevatorFloor - topFloor + elevatorFloors}s`
  const translateY = (topFloor - props.elevatorFloor) * elevatorFloorRowHeight
  return {
    transform: `translateY(${translateY}px)`,
    transition: `transform ${transitionDuration} linear`
  }
})
</script>

<style lang="scss" scoped>
.elevatorShaft {
  background-color: rgb(91, 244, 255);
  height: 100px;
  width: 100%;
}
</style>
