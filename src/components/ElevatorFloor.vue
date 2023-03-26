<template>
  <div class="elevatorFloor">
    <span>{{ elevatorFloorId }}</span>
    <button
      :style="{ borderColor: !props.isActive ? 'rgb(75, 196, 205)' : 'rgb(255, 151, 60)' }"
      @click="() => $emit('floorHandler', elevatorFloorId)"
    >
      <svg width="26" height="26">
        <circle
          cx="13"
          cy="13"
          r="8"
          :stroke="!props.isActive ? 'rgb(75, 196, 205)' : 'rgb(255, 151, 60)'"
          stroke-width="1.5"
          fill="none"
        />
        <circle
          cx="13"
          cy="13"
          r="4"
          :stroke="!props.isActive ? 'rgb(75, 196, 205)' : 'rgb(255, 151, 60)'"
          :fill="!props.isActive ? 'rgb(75, 196, 205)' : 'rgb(255, 151, 60)'"
        />
      </svg>
    </button>
  </div>
</template>

<script setup>
import { elevatorFloors } from '../config.js'

const props = defineProps(['elevatorFloorId', 'isActive'])
const elevatorFloorRow = `${elevatorFloors - props.elevatorFloorId + 1} / ${
  elevatorFloors - props.elevatorFloorId + 2
}`
</script>

<style lang="scss" scoped>
.elevatorFloor {
  grid-row: v-bind(elevatorFloorRow);
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 5px;
  span {
    font-family: Inter, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu,
      Cantarell, 'Fira Sans', 'Droid Sans', 'Helvetica Neue', sans-serif;
    font-size: 18px;
    font-weight: bold;
  }
  button {
    padding: 0;
    background-color: inherit;
    border-radius: 4px;
    width: 30px;
    height: 30px;
    cursor: pointer;
  }
}
</style>
