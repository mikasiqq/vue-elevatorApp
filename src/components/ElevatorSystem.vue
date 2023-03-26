<script setup>
import { computed, reactive } from 'vue'
import ElevatorStall from '@/components/ElevatorStall.vue'
import ElevatorFloor from '@/components/ElevatorFloor.vue'

import { elevatorStalls, elevatorFloors } from '../config.js'

const elevatorFloorsColumn = computed(() => `${elevatorStalls + 1} / ${elevatorStalls + 2}`)
const elevatorFloorsRow = computed(() => `1 / ${elevatorFloors + 1}`)

const elevators = reactive([])
const floors = reactive([])

const restTime = 3000

for (let i = 0; i < elevatorStalls; i++) {
  elevators.push({
    id: i,
    isAvailable: true,
    currentFloor: 1,
    state: 'idle',
    destinationFloor: 1,
    resting: false
  })
}

for (let i = 0; i < elevatorFloors; i++) {
  floors.push({
    id: i + 1,
    isActive: false
  })
}

function findClosestAvailableElevator(floor) {
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

  return closestElevator
}

function isElevatorAvailable(elevator, floor) {
  return elevator.isAvailable && elevator.currentFloor !== floor && !floors[floor - 1].isActive
}

function setElevatorState(elevator, floor) {
  elevator.isAvailable = false
  elevator.destinationFloor = floor
  elevator.state = elevator.currentFloor < floor ? 'goingUp' : 'goingDown'
}
function goToFloor(floor) {
  const closestElevator = findClosestAvailableElevator(floor)

  if (!closestElevator || !isElevatorAvailable(closestElevator, floor)) return

  setElevatorState(closestElevator, floor)

  const floorIndex = floor - 1
  floors[floorIndex].isActive = true

  const stopTime = Math.abs(closestElevator.currentFloor - floor) * 1000
  setTimeout(() => {
    closestElevator.currentFloor = closestElevator.destinationFloor
    closestElevator.state = closestElevator.resting ? 'idle' : 'resting'
    closestElevator.isAvailable = true
    closestElevator.resting = !closestElevator.resting

    if (floorIndex !== -1) {
      floors[floorIndex].isActive = false
    }
  }, stopTime + restTime)
}
</script>

<template>
  <div class="elevatorSystem">
    <ElevatorStall
      class="elevatorStall"
      v-for="elevatorStall in elevators"
      :key="elevatorStall.id"
      :elevatorId="elevatorStall.id"
      :elevatorFloor="elevatorStall.destinationFloor"
      :elevatorState="elevatorStall.state"
      :elevatorResting="elevatorStall.resting"
    />
    <div class="elevatorFloors">
      <ElevatorFloor
        v-for="elevatorFloor in floors"
        :key="elevatorFloor.id"
        :elevatorFloorId="elevatorFloor.id"
        :isActive="elevatorFloor.isActive"
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
