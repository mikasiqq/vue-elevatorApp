<template>
  <div
    :class="['elevatorShaft', { elevatorShaftResting: props.elevatorResting }]"
    :style="[elevatorShaftStyle]"
  >
    <ElevatorInfo
      v-if="elevatorState === 'goingUp' || elevatorState === 'goingDown'"
      :elevatorState="props.elevatorState"
      :destinationFloor="props.elevatorFloor"
    />
  </div>
</template>

<script setup>
import { computed } from 'vue'
import { elevatorFloors } from '../config.js'

import ElevatorInfo from '@/components/ElevatorInfo.vue'

const props = defineProps(['elevatorFloor', 'elevatorState', 'elevatorResting'])

const elevatorFloorRowHeight = 100
const topFloor = elevatorFloors

const elevatorShaftStyle = computed(() => {
  const translateY = (topFloor - props.elevatorFloor) * elevatorFloorRowHeight
  return {
    transform: `translateY(${translateY}px)`
  }
})
</script>

<style lang="scss" scoped>
.elevatorShaft {
  background-color: rgb(91, 244, 255);
  height: 100px;
  width: 100%;
  transition: transform 1s linear;

  &Resting {
    animation-duration: 1s;
    animation-name: blink;
    animation-iteration-count: infinite;
    animation-timing-function: linear;
  }
}

@keyframes blink {
  0% {
    opacity: 1;
  }
  50% {
    opacity: 0.5;
  }
  100% {
    opacity: 1;
  }
}
</style>
