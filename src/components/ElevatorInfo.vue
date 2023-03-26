<template>
  <div class="elevatorInfo">
    <svg
      v-if="props.elevatorState === 'goingUp'"
      xmlns="http://www.w3.org/2000/svg"
      height="18px"
      viewBox="0 0 24 24"
      width="18px"
      fill="#FFFFFF"
    >
      <path d="M0 0h24v24H0V0z" fill="none" />
      <path d="M4 12l1.41 1.41L11 7.83V20h2V7.83l5.58 5.59L20 12l-8-8-8 8z" />
    </svg>
    <svg
      v-if="props.elevatorState === 'goingDown'"
      xmlns="http://www.w3.org/2000/svg"
      height="18px"
      viewBox="0 0 24 24"
      width="18px"
      fill="#FFFFFF"
    >
      <path d="M0 0h24v24H0V0z" fill="none" />
      <path d="M20 12l-1.41-1.41L13 16.17V4h-2v12.17l-5.58-5.59L4 12l8 8 8-8z" />
    </svg>

    <div class="floorNumber">{{ props.destinationFloor }}</div>
  </div>
</template>
<script setup>
import { defineProps } from 'vue'

const props = defineProps(['elevatorState', 'destinationFloor'])
</script>
<style lang="scss" scoped>
.elevatorInfo {
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  gap: 5px;
  margin: 10px auto;
  background-color: #000;
  opacity: 0.5;
  width: 50%;
  height: 30px;
  border-radius: 5px;
  color: white;
}

.floorNumber {
  font-weight: bold;
  font-family: Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu,
    Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
}

.directionArrows {
  display: flex;
  justify-content: space-between;
  align-items: center;
  width: 100%;
}
</style>
