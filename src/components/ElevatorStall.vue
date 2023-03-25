<template>
  <div class="elevatorStall">
    <ElevatorShaft :elevatorFloor="props.elevatorFloor" />
  </div>
</template>

<script setup>
import { computed } from 'vue'
import ElevatorShaft from '@/components/ElevatorShaft.vue'

import { elevatorFloors } from '../config.js'

const props = defineProps(['elevatorFloor', 'elevatorId'])

const elevatorStallColumn = computed(() => `${props.elevatorId + 1} / ${props.elevatorId + 2}`)
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
