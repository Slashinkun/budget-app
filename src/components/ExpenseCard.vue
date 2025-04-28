<script setup>
defineProps({ expense: Object })
</script>

<template>
  <h3>{{ expense.amount }}</h3>
  <p>{{ expense.description }}</p>
  <p>{{ expense.category }}</p>
  <p>{{ expense.date }}</p>
</template>

<style scoped></style>
