<script setup>
import { ref, computed } from 'vue'
import Category from './components/Category.vue'
import ExpenseCard from './components/ExpenseCard.vue'

const categories = ref([])
const newCategory = ref('')

const expenses = ref([])

const newExpense = ref({
  amount: 0,
  category: '',
  description: '',
  date: '',
})

const addCategories = () => {
  if (categories.value.some((cat) => cat === newCategory.value)) {
    return
  } else {
    categories.value.push(newCategory.value)
  }
}

const addExpense = () => {
  console.log(newExpense.value)

  if (newExpense.value.amount === 0) {
    return
  }
  if (newExpense.value.date === null) {
    return
  }

  expenses.value.push({ ...newExpense.value })
  console.log(expenses)

  newExpense.value.amount = 0
  newExpense.value.date = ''
  newExpense.value.category = ''
  newExpense.value.description = ''
}
</script>

<template>
  <input type="text" v-model.trim="newCategory" @keyup.enter="addCategories" class="" />
  <div v-for="cat in categories">
    <Category :title="cat"></Category>
  </div>
  <div>
    <form>
      <label for="amount"> Amount</label>
      <input type="number" min="1" v-model="newExpense.amount" />
      <label for="description">Description</label>
      <textarea v-model="newExpense.description"></textarea>
      <label for="categories">Category</label>
      <select name="" id="">
        <option v-for="cat in categories" :value="cat">{{ cat }}</option>
      </select>
      <label for="date">Date</label>
      <input type="date" v-model="newExpense.date" />
      <button type="submit" class="btn btn-primary" @click.prevent="addExpense">Add</button>
    </form>
  </div>
  <div v-for="expense in expenses">
    <ExpenseCard :expense="expense"></ExpenseCard>
  </div>
</template>

<style scoped></style>
