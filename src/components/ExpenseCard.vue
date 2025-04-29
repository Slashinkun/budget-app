<script setup>
defineProps({ expense: Object })
const emit = defineEmits(['delete'])
</script>

<template>
  <div class="container">
    <div class="category-description">
      <div>{{ expense.category }}</div>
      <div>{{ expense.description }}</div>
      <div>{{ expense.date }}</div>
    </div>
    <div class="actions">
      <div class="amount">{{ expense.amount }} $</div>
      <button class="btn btn-danger" @click="$emit('delete')">Delete</button>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: flex-start;
  border: 1px solid;
  margin: 10px auto;
}

.category-description {
  display: flex;
  flex-direction: column;
}

.actions {
  display: flex;
}
</style>
