<template>
  <div class="elevatorFloor">
    <span>{{ elevatorFloorId }}</span>
    <button @click="() => $emit('floorHandler', elevatorFloorId)">
      <svg width="26" height="26">
        <circle cx="13" cy="13" r="8" stroke="rgb(75, 196, 205)" stroke-width="1.5" fill="none" />
        <circle cx="13" cy="13" r="4" stroke="rgb(75, 196, 205)" fill="rgb(75, 196, 205)" />
      </svg>
    </button>
  </div>
</template>

<script setup>
import { elevatorFloors } from '../config.js'

const props = defineProps(['elevatorFloorId'])
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
    border: 2px solid rgb(75, 196, 205);
    border-radius: 4px;
    width: 30px;
    height: 30px;
    cursor: pointer;
  }
}
</style>
