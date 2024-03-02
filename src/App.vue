<template>
  <div class="container">
    <Header />
    <Balance :total="total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionsList :transactions="transactions" @transactionDeleted="handelTransactionDeleted"/>
    <AddTransaction @transactionSubmitted="handelTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionsList from './components/TransactionsList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { ref, computed, onMounted } from 'vue';
import { useToast } from 'vue-toastification'

const toast = useToast();

const transactions = ref([
  // { id: 1, text: 'Flower', amount: -19.99 },
  // { id: 2, text: 'Salary', amount: 1000 },
  // { id: 3, text: 'Book', amount: -20.99 },
  // { id: 4, text: 'tax', amount: -180 },
]);


onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'))
  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
})

const total = computed(() => {
  return transactions.value?.reduce((acc, cur) => acc += cur.amount, 0)
});

const income = computed(() => {
  return transactions.value?.filter(t => t.amount > 0)?.reduce((acc, cur) => acc += cur.amount, 0)
});

const expenses = computed(() => {
  return transactions.value?.filter(t => t.amount < 0)?.reduce((acc, cur) => acc += cur.amount, 0)
});

function generateUniqueId() {
  return Math.floor(Math.random() * 100000);
}

function handelTransactionSubmitted(d) {
  transactions.value.push({
    id: generateUniqueId(),
    text: d.text,
    amount: d.amount
  });

  updateLocalStorage();

  toast.success('Transaction added')
}

const handelTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(t => t.id !== id);
  updateLocalStorage();
  toast.success('Transaction deleted')
}

function updateLocalStorage() {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
}

</script>
