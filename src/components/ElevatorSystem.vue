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

  if (closestElevator.currentFloor === floor) return

  const floorIndex = floors.findIndex((el) => el.id === floor)
  if (floorIndex !== -1) {
    floors[floorIndex].isActive = true
  }

  if (closestElevator) {
    closestElevator.isAvailable = false
    closestElevator.destinationFloor = floor
    closestElevator.state = closestElevator.currentFloor < floor ? 'goingUp' : 'goingDown'
    setTimeout(() => {
      closestElevator.currentFloor = closestElevator.destinationFloor

      if (closestElevator.resting) {
        setTimeout(() => {
          closestElevator.state = 'idle'
          closestElevator.isAvailable = true
          closestElevator.resting = false

          if (floorIndex !== -1) {
            floors[floorIndex].isActive = false
          }
        }, restTime)
      } else {
        closestElevator.state = 'resting'
        closestElevator.resting = true

        setTimeout(() => {
          closestElevator.state = 'idle'
          closestElevator.isAvailable = true
          closestElevator.resting = false

          if (floorIndex !== -1) {
            floors[floorIndex].isActive = false
          }
        }, restTime)
      }
    }, Math.abs(closestElevator.currentFloor - floor) * 1000)
  } else {
    setTimeout(() => {
      goToFloor(floor)
    }, 1000)
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
