<script setup>
import HelloWorld from './components/Main.vue'
import axios from 'axios'

axios.defaults.baseURL = "http://localhost:3000"
</script>

<template>
  <div class="max-w-screen-lg lg:mx-auto mx-2">
    <h1 class="text-5xl">Transcripción</h1>
    <HelloWorld />
  </div>
</template>

<style scoped></style>
