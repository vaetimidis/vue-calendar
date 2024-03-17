<script setup lang="ts">
import Calendar from './components/Calendar.vue'

const selectedDate = ''
</script>

<template>
  <Calendar :date="selectedDate" />
</template>
