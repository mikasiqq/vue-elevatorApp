<template>
  <div class="elevatorShaft" :style="elevatorShaftStyle">
    <ElevatorInfo
      v-if="elevatorState === 'goingUp' || elevatorState === 'goingDown'"
      :elevatorState="props.elevatorState"
      :destinationFloor="props.elevatorFloor"
    />
  </div>
</template>
<script setup>
import { computed } from 'vue'

import ElevatorInfo from '@/components/ElevatorInfo.vue'

const props = defineProps(['elevatorFloor', 'elevatorState'])
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
