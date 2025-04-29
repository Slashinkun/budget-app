<script setup>
import { ref, computed } from 'vue'
import ExpenseCard from './components/ExpenseCard.vue'

const localStorageUserCategory = localStorage.getItem('user_categories')
const categories = ref(
  localStorageUserCategory
    ? JSON.parse(localStorageUserCategory)
    : ['Health', 'Food', 'Travel', 'House', 'Leisure'],
)
const newCategory = ref('')

const remainingBudget = computed(() => {
  let sumExpenses = expenses.value.reduce((acc, expense) => acc + expense.amount, 0)

  return userBudget.value - sumExpenses
})

const localStorageExpenses = localStorage.getItem('user_expenses')
const expenses = ref(localStorageExpenses ? JSON.parse(localStorageExpenses) : [])

const newUserBudget = ref(0)
const userBudget = ref(
  localStorage.getItem('user_budget') ? JSON.parse(localStorage.getItem('user_budget')) : 0,
)
const validateBudget = () => {
  if (newUserBudget.value > 0) {
    localStorage.setItem('user_budget', newUserBudget.value)
    newUserBudget.value = 0
  }
}

const newExpense = ref({
  id: expenses.value.length + 1,
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
    localStorage.setItem('user_categories', JSON.stringify(categories.value))
    newCategory.value = ''
  }
}

const addExpense = () => {
  console.log(newExpense.value)

  if (!newExpense.value.amount || !newExpense.value.date) {
    return
  }

  expenses.value.push({ ...newExpense.value })
  console.log(expenses)

  localStorage.setItem('user_expenses', JSON.stringify(expenses.value))

  newExpense.value = {
    amount: 0,
    date: '',
    category: '',
    description: '',
  }
}

const deleteExpense = (id) => {
  const index = expenses.value.indexOf(id)
  expenses.value.splice(index, 1)
  localStorage.setItem('user_expenses', JSON.stringify(expenses.value))
}
</script>

<template>
  <h1>Budget App</h1>
  <h2>
    Your budget : <span>{{ userBudget }} $</span>
  </h2>
  <h3>
    Remaining :
    <span
      v-bind:class="{
        'negative-budget': remainingBudget < 0,
        'positive-budget': remainingBudget > userBudget * 0.75,
        'average-budget': remainingBudget < userBudget * 0.75,
      }"
      >{{ remainingBudget }} $
    </span>
  </h3>

  <div class="container">
    <h2>Add a category</h2>
    <div><input type="text" v-model.trim="newCategory" @keyup.enter="addCategories" /></div>
    <input type="number" placeholder="Enter your budget" v-model="newUserBudget" />
    <button class="btn btn-success" @click="validateBudget">Validate</button>
  </div>

  <div>
    <h2>Add an expense</h2>
    <form class="expense-form" @submit.prevent="addExpense" id="expense-form">
      <label for="expense_amount"> Amount</label>
      <input
        type="number"
        id="expense_amount"
        min="1"
        v-model.number="newExpense.amount"
        required
      />
      <label for="expense_desc">Description</label>
      <input v-model="newExpense.description" id="expense_desc" placeholder="For what ?" />
      <label for="expense_category">Category</label>
      <select v-model="newExpense.category" id="expense_category">
        <option value=""></option>
        <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
      </select>
      <label for="expense_date">Date</label>
      <input type="date" id="expense_date" v-model="newExpense.date" required />
      <button
        type="submit"
        class="btn btn-primary"
        v-bind:disabled="newExpense.amount === 0 || newExpense.amount === ''"
      >
        Add
      </button>
    </form>
  </div>
  <div v-for="expense in expenses">
    <ExpenseCard
      :expense="expense"
      @delete="deleteExpense(expense.id)"
      :key="expense.id"
    ></ExpenseCard>
  </div>
</template>

<style scoped>
.expense-form {
  margin: auto;
}

.expense-form::after {
  margin: 4px;
}

.negative-budget {
  color: red;
}

.positive-budget {
  color: green;
}

.average-budget {
  color: orange;
}
</style>
