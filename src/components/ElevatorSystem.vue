<script setup>
import { computed, reactive } from 'vue'
import ElevatorStall from '@/components/ElevatorStall.vue'
import ElevatorFloor from '@/components/ElevatorFloor.vue'

import { elevatorStalls, elevatorFloors } from '../config.js'

const elevatorFloorsColumn = computed(() => `${elevatorStalls + 1} / ${elevatorStalls + 2}`)
const elevatorFloorsRow = computed(() => `1 / ${elevatorFloors + 1}`)

const elevators = reactive([])
for (let i = 0; i < elevatorStalls; i++) {
  elevators.push({
    id: i,
    isAvailable: true,
    currentFloor: 1,
    state: 'idle'
  })
}
function goToFloor(floor) {
  let closestElevator = null
  let closestDistance = Infinity
  for (const elevator of elevators) {
    if (elevator.isAvailable) {
      const distance = Math.abs(elevator.currentFloor - floor)
      if (distance < closestDistance) {
        closestElevator = elevator
        closestDistance = distance
      }
    }
  }

  if (closestElevator) {
    closestElevator.isAvailable = false
    closestElevator.state = closestElevator.currentFloor < floor ? 'goingUp' : 'goingDown'
    setTimeout(() => {
      closestElevator.currentFloor = floor
      closestElevator.state = 'idle'
      closestElevator.isAvailable = true
    }, Math.abs(closestElevator.currentFloor - floor) * 1000)
  }
}
</script>

<template>
  <div class="elevatorSystem">
    <ElevatorStall
      class="elevatorStall"
      v-for="elevatorStall in elevators"
      :key="elevatorStall.id"
      :elevatorId="elevatorStall.id"
      :elevatorFloor="elevatorStall.currentFloor"
    />
    <div class="elevatorFloors">
      <ElevatorFloor
        v-for="elevatorFloor in elevatorFloors"
        :key="elevatorFloor"
        :elevatorFloorId="elevatorFloor"
        @floorHandler="goToFloor"
      />
    </div>
  </div>
</template>

<style lang="scss" scoped>
.elevatorSystem {
  width: fit-content;
  height: fit-content;
  display: grid;

  grid-template-rows: repeat(v-bind(elevatorFloors), 100px);
  grid-template-columns: repeat(v-bind(elevatorStalls) + 1, 60px);
}
.elevatorStall {
  display: grid;
  grid-row: v-bind(elevatorFloorsRow);
  grid-column: 1 / (v-bind(elevatorStalls) + 1);
}
.elevatorFloors {
  display: grid;
  grid-row: v-bind(elevatorFloorsRow);
  grid-column: v-bind(elevatorFloorsColumn);
}
</style>
