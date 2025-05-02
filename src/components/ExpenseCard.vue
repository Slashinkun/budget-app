<script setup>
defineProps({ expense: Object })
const emit = defineEmits(['delete', 'edit'])
</script>

<template>
  <div class="container border rounded-4 border-dark">
    <div class="category-description">
      <div class="fw-medium">{{ expense.category }}</div>
      <div class="fw-semibold">{{ expense.description }}</div>
      <div class="fw-light">{{ expense.date }}</div>
    </div>
    <div class="actions">
      <div class="amount">
        <b>{{ expense.amount }} $</b>
      </div>
      <div class="action-buttons">
        <button class="btn btn-danger" @click="$emit('delete')">Delete</button>
        <button class="btn btn-secondary" @click="$emit('edit')">Edit</button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
  align-items: flex-start;
  margin: 10px auto;
}

.category-description {
  display: flex;
  flex-direction: column;
}

.actions {
  display: flex;
  flex-direction: column;
  padding: 4px auto;
}

.amount {
  font-size: larger;
}
</style>
