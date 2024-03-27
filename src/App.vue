<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionsList
      :transactions="transactions"
      @delete-transaction="handleTransactionDeleted"
    />
    <AddTransaction @add-transaction="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Balance from "./components/Balance.vue";
import Header from "./components/Header.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionsList from "./components/TransactionsList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { ref, computed, onMounted } from "vue";
import { useToast } from "vue-toastification";

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  transactions.value = JSON.parse(localStorage.getItem("transactions")) || [];
});

const total = computed(() => {
  return transactions.value
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2);
});

const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => (acc += transaction.amount), 0)
    .toFixed(2);
});

const expenses = computed(() => {
  return (
    transactions.value
      .filter((transaction) => transaction.amount < 0)
      .reduce((acc, transaction) => (acc += transaction.amount), 0) * -1
  ).toFixed(2);
});

const handleTransactionSubmitted = (newTransaction) => {
  transactions.value.push(newTransaction);
  saveTransactions();
  toast.success("Transaction added successfully!");
};

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  saveTransactions();
  toast.success("Transaction deleted successfully!");
};

const saveTransactions = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>
