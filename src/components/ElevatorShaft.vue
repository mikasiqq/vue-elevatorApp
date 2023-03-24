<template>
  <div class="elevatorShaft"></div>
</template>

<script setup>
import { computed } from 'vue'

import { elevatorFloors } from '../config.js'

const elevatorShaftColumn = computed(() => `${elevatorFloors + 1} / ${elevatorFloors + 2}`)
</script>

<style lang="scss" scoped>
.elevatorShaft {
  grid-row: v-bind(elevatorShaftColumn);
  background-color: rgb(91, 244, 255);
}
</style>
