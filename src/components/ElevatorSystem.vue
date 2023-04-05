<script setup>
import { computed, reactive } from 'vue'

import ElevatorStall from '@/components/ElevatorStall.vue'
import ElevatorFloor from '@/components/ElevatorFloor.vue'

import { elevatorStalls, elevatorFloors, restTime } from '../config.js'

const elevatorFloorsColumn = computed(() => `${elevatorStalls + 1} / ${elevatorStalls + 2}`)
const elevatorFloorsRow = computed(() => `1 / ${elevatorFloors + 1}`)

const elevators = reactive([])
const floors = reactive([])
const queue = reactive([])

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

  if (!closestElevator) {
    const notAvailableElevator = elevators.find((elevator) => elevator.id === queue[0].elevatorId)
    queue.shift()
    queue.push({ elevatorId: notAvailableElevator.id, floorId: floor })
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

function makeElevatorRest(elevator, floorIndex) {
  setTimeout(() => {
    elevator.state = 'idle'
    elevator.isAvailable = true
    elevator.resting = false
    if (floorIndex !== -1) {
      floors[floorIndex].isActive = false
    }
    queue.shift()

    const elevatorInQueue = queue.find((q) => q.elevatorId === elevator.id)
    if (elevatorInQueue) {
      floors[elevatorInQueue.floorId - 1].isActive = true

      setTimeout(() => {
        elevator.destinationFloor = elevatorInQueue.floorId
        elevator.state = elevator.currentFloor < elevator.destinationFloor ? 'goingUp' : 'goingDown'

        setTimeout(() => {
          elevator.currentFloor = elevator.destinationFloor

          elevator.state = 'resting'
          elevator.resting = true
          makeElevatorRest(elevator, elevatorInQueue.floorId - 1)
        }, Math.abs(elevator.currentFloor - elevatorInQueue.floorId) * 1000)
        console.log(queue[0], elevator, 'el')
      }, restTime)
    }
    console.log(elevators)
  }, restTime)
}

function goToFloor(floor) {
  const closestElevator = findClosestAvailableElevator(floor)

  if (!closestElevator || !isElevatorAvailable(closestElevator, floor)) return

  setElevatorState(closestElevator, floor)

  queue.push({ elevatorId: closestElevator.id, floorId: floor })
  queueHandler()
}

function queueHandler() {
  if (queue.length > 0) {
    for (const el of queue) {
      const elevator = elevators.find((elevator) => elevator.id === el.elevatorId)
      const floor = floors.find((floor) => floor.id === el.floorId)

      floors[floor.id - 1].isActive = true

      setTimeout(() => {
        elevator.currentFloor = elevator.destinationFloor

        elevator.state = 'resting'
        elevator.resting = true
        makeElevatorRest(elevator, floor.id - 1)
      }, Math.abs(elevator.currentFloor - floor.id) * 1000)
    }
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
