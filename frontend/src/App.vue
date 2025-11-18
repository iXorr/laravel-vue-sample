<script setup>
  import { ref } from 'vue';

  const backend_port = 80;
  const backend_api_url = `http://localhost:${backend_port}/api`;
  
  const message = ref('Hello!');

  async function fetchServer() {
    const res = await fetch(`${backend_api_url}/test`);
    message.value = (await res.json()).message;
  }
</script>

<template>
  <button @click="fetchServer">Push me!</button>
  
  <div>
    {{ message }}
  </div>
</template>
