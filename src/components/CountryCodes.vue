<script setup>
import codes from '../data/codes.json';
import { ref } from 'vue';
</script>

<template>
  <h1>Códigos:</h1>
  <div>
    <ul>
      <li v-for="code in codes" :key="code.code">
        {{ code.code }}
      </li>
    </ul>
  </div>
</template>