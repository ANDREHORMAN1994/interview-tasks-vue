<script setup lang="ts">
import { ref, watch } from 'vue';

// Creating a state with a number value and a boolean to indicate whether it is a divisor
type NumberItem = {
  value: number;
  isDivisor: boolean;
};

// Using ref to set reactive state
const limit = ref<number>(100);

// Renaming functions and variables to be more descriptive
function changeListOfNumbers() {
  let newListOfNumbers: NumberItem[] = [];

  for (let i = 0; i < limit.value; i += 1) {
    const newItem = { value: i, isDivisor: false };
    newListOfNumbers = [...newListOfNumbers, newItem];
  }

  return newListOfNumbers.sort(() => Math.random() - 0.5);
}

// Using ref to calculate list of numbers reactively
const numbers = ref<NumberItem[]>(changeListOfNumbers());

// Removing functions that manipulated the DOM directly, using reactive state for hover control
function hoverNumber(number: number) {
  for (let i = 0; i < numbers.value.length; i++) {
    const num = numbers.value[i];
    if (number % num.value === 0) {
      numbers.value[i].isDivisor = true;
    } else {
      numbers.value[i].isDivisor = false;
    }
  }
}

// Removing direct DOM manipulation, updating numbers state
function reset() {
  numbers.value.forEach((num) => {
    num.isDivisor = false;
  });
}

// Watching limit changes and updating the list of numbers
watch(
  limit,
  () => {
    numbers.value = changeListOfNumbers();
  },
  { deep: true, immediate: true },
);
</script>

<template>
  <div>
    <input type="number" v-model="limit" />
    <br />
    <br />
    <div
      v-for="numberObj in numbers"
      :key="numberObj.value"
      :id="`number-${numberObj.value}`"
      :class="['number', { active: numberObj.isDivisor }]"
      @mouseover="hoverNumber(numberObj.value)"
      @mouseout="reset"
    >
      {{ numberObj.value }}
    </div>
  </div>
</template>

<style scoped>
.number {
  display: inline-block;
  padding: 5px;
  background-color: rgba(211, 211, 211, 0.527);
  margin: 5px;
}

.active {
  background-color: red;
}
</style>
