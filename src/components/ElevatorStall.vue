<template>
  <div class="elevatorStall">
    <ElevatorShaft />
  </div>
</template>

<script setup>
import ElevatorShaft from '@/components/ElevatorShaft.vue'

import { elevatorFloors } from '../config.js'

const props = defineProps(['elevatorStallId'])

const elevatorStallColumn = `${props.elevatorStallId} / ${props.elevatorStallId + 1}`
</script>

<style lang="scss" scoped>
.elevatorStall {
  display: grid;
  grid-column: v-bind(elevatorStallColumn);
  grid-template-rows: repeat(v-bind(elevatorFloors), 100px);
  grid-template-columns: 100px;

  margin: 0 5px;
  border-left: 2px solid rgb(215, 215, 215);
  border-right: 2px solid rgb(215, 215, 215);
}
</style>
