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

const filter_category = ref('')

const searchFilter = ref('')

const dateFilter = ref('')

const editingExpenseId = ref(null)

const editedExpense = ref({ amount: 0, category: '', description: '', date: '' })

const remainingBudget = computed(() => {
  let sumExpenses = expenses.value.reduce((acc, expense) => acc + expense.amount, 0)

  return userBudget.value - sumExpenses
})

const amountSpent = computed(() => {
  return expenses.value.reduce((acc, expense) => acc + expense.amount, 0)
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

const startEdit = (expense) => {
  editingExpenseId.value = expense.id
  editedExpense.value = { ...expense }
}

const saveEditedExpense = (id) => {
  const index = expenses.value.findIndex((expense) => expense.id === id)

  if (index !== -1) {
    expenses.value[index] = { ...editedExpense.value }
    localStorage.setItem('user_expenses', JSON.stringify(expenses.value))
  }

  editingExpenseId.value = null
}

const cancelEdit = () => {
  editingExpenseId.value = null
}

const filteredExpenses = computed(() => {
  let filteredList = expenses.value

  if (filter_category.value && filter_category.value.trim() !== '') {
    filteredList = filteredList.filter((expense) => {
      return (
        expense.category &&
        expense.category.toLowerCase().trim() === filter_category.value.toLowerCase().trim()
      )
    })
  }

  if (searchFilter.value && searchFilter.value.trim() !== '') {
    filteredList = filteredList.filter((expense) => {
      return (
        expense.description &&
        expense.description.toLowerCase().includes(searchFilter.value.toLowerCase().trim())
      )
    })
  }

  if (dateFilter.value && dateFilter.value.trim() !== '') {
    filteredList = filteredList.filter((expense) => {
      return expense.date && expense.date === dateFilter.value
    })
  }

  return filteredList
})

const resetFilters = () => {
  filter_category.value = ''
  searchFilter.value = ''
}
</script>

<template>
  <div class="container mt-4 p-4">
    <div class="row p-3">
      <div class="col-md-6">
        <h1>Budget App</h1>
        <h3>
          Your budget : <span>{{ userBudget }}$</span>
        </h3>
      </div>
      <div class="col md-6 text-end">
        <h4>
          Remaining :
          <span
            v-bind:class="{
              'negative-budget': remainingBudget < 0,
              'positive-budget': remainingBudget > userBudget * 0.75,
              'average-budget': remainingBudget < userBudget * 0.75,
            }"
            >{{ remainingBudget }} $
          </span>
        </h4>
        <h5>
          Spent : <span>{{ amountSpent }} $</span>
        </h5>
      </div>
    </div>

    <div class="row mb-4">
      <div class="col-md-6">
        <h2>Add a category</h2>
        <input
          class="form-control"
          type="text"
          v-model.trim="newCategory"
          @keyup.enter="addCategories"
        />
      </div>
      <div class="col-md-6">
        <h3>Update your budget</h3>
        <div class="input-group">
          <input
            type="number"
            placeholder="Enter your new budget"
            class="form-control"
            v-model="newUserBudget"
            inputmode="numeric"
          />
          <button class="btn btn-success" @click="validateBudget">Validate</button>
        </div>
      </div>
    </div>
  </div>
  <div class="row mb-4">
    <div class="col p-4 m-4">
      <h2>Add an expense</h2>
      <form class="expense-form" @submit.prevent="addExpense" id="expense-form">
        <div class="mb-3">
          <label for="expense_amount"> Amount</label>
          <input
            type="number"
            id="expense_amount"
            min="1"
            v-model.number="newExpense.amount"
            required
            class="form-control"
            inputmode="numeric"
          />
        </div>
        <div class="mb-3">
          <label for="expense_desc">Description</label>
          <input
            class="form-control"
            v-model="newExpense.description"
            id="expense_desc"
            placeholder="For what ?"
          />
        </div>
        <div class="mb-3">
          <label for="expense_category">Category</label>
          <select class="form-select" v-model="newExpense.category" id="expense_category">
            <option value=""></option>
            <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
          </select>
        </div>
        <div class="mb-3">
          <label for="expense_date">Date</label>
          <input
            class="form-control"
            type="date"
            id="expense_date"
            v-model="newExpense.date"
            required
          />
        </div>
        <button
          type="submit"
          class="btn btn-primary m-2"
          v-bind:disabled="newExpense.amount === 0 || newExpense.amount === ''"
        >
          Add
        </button>
      </form>
    </div>

    <div class="row m-4">
      <div class="col">
        <h4>Expenses</h4>
        <div class="filters">
          <label>Filter : </label>
          <select id="filter_category" v-model="filter_category">
            <option value=""></option>
            <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
          </select>
          <input type="text" placeholder="Search by description" v-model="searchFilter" />
          <input type="date" v-model="dateFilter" />
          <button class="btn btn-danger" @click="resetFilters">Reset Filters</button>
        </div>
      </div>
      <div v-if="expenses.length === 0">No expenses</div>
      <div v-for="expense in filteredExpenses" :key="expense.id" class="mb-2">
        <div
          v-if="expense.id === editingExpenseId"
          class="m-3 border rounded-4 border-dark p-2 editing-form"
        >
          <input type="number" min="1" v-model="editedExpense.amount" />
          <input type="text" v-model="editedExpense.description" />
          <select v-model="editedExpense.category">
            <option value=""></option>
            <option v-for="cat in categories" :key="cat" :value="cat">{{ cat }}</option>
          </select>
          <input type="date" v-model="editedExpense.date" />
          <button class="btn btn-success" @click="saveEditedExpense(editingExpenseId)">Save</button>
          <button class="btn btn-danger" @click="cancelEdit">Cancel</button>
        </div>
        <div v-else>
          <ExpenseCard
            :expense="expense"
            @delete="deleteExpense(expense.id)"
            @edit="startEdit(expense)"
            :key="expense.id"
          ></ExpenseCard>
        </div>
      </div>
    </div>
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

.filters {
  display: flex;
  flex-direction: row;
  justify-content: space-evenly;
}

.editing-form {
  display: flex;
  flex-direction: row;
}
</style>
