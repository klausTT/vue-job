<script setup lang="ts">
import { reactive, ref } from 'vue'
import SetupCom from '../components/exmple/SetupCom.vue'
import BasicCom from '../components/exmple/BasicCom.vue'
const count = ref(0)
const person = reactive({
  name: 'wudt',
  age: 18
})

const reset = () => {
  count.value = 0
}

const addFn = () => {
  count.value++
}
</script>

<template>
  <div class="about">
    <h1>This is an about page</h1>
    <p>{{ count }}</p>
    <button @click="addFn">add</button>
    <button @click="reset">reset</button>
    <SetupCom />
    <BasicCom />
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    display: flex;
    align-items: center;
    flex-direction: column;
  }
}
</style>
