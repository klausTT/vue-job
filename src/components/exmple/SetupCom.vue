<template>
  <div>setup count: {{ original.count }}</div>
  <button @click="changeCount">change subName</button>

  <button @click="changePersonInfo">changePersonInfo</button>
  <p>{{ person.name }} - {{ person.age }}</p>
</template>

<script lang="ts" setup>
import { reactive, readonly, ref, watchEffect } from 'vue'

const person = ref({ name: 'wudt', age: 18 })
const original = reactive({ count: 0 })

const copy = readonly(original)

watchEffect(() => {
  // 用来做响应性追踪
  console.log('copy.count: ', copy.count)
  console.log('person: ', person.value)
})

const changeCount = () => {
  // 更改源属性会触发其依赖的侦听器
  original.count++
}

const changePersonInfo = () => {
  person.value = {
    name: '开玩笑',
    age: 25
  }
}
</script>

<style></style>
