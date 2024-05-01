<template>
  <div>
    <Header title="Expense Tracker" />
    <Balance :total="+total" />
    <IncomeExpense :income="+income" :expense="+expense" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpense from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";
import { ref, onMounted } from "vue";
// This ref allows a component or a group of data to be reactive
import { computed } from "vue";
// Thus allows us create reactive and cached values
import { useToast } from "vue-toastification";

const toast = useToast();

const transactions = ref([
  // {
  //   id: 1,
  //   text: "Flower",
  //   amount: -19.99,
  // },
  // {
  //   id: 2,
  //   text: "Salary",
  //   amount: 299.97,
  // },
  // {
  //   id: 3,
  //   text: "Book",
  //   amount: -10,
  // },
  // {
  //   id: 4,
  //   text: "Camea",
  //   amount: 150,
  // },
]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

// save to localStorage

const saveTransactionToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};

// Get Total
const total = computed(() => {
  // We are doing this (transactions.value) because the transactions array has been wrapped with the ref property
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
  // The zero is passed in to ensure the accumulator starts from zero
});

// Get Income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0);
});

// Get Expenses
const expense = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0);
});

// Add Transaction

const handleTransactionSubmitted = (transactionData) => {
  // console.log(transactionData, "TransactionData");
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });
  saveTransactionToLocalStorage();
  toast.success("Transaction added");
};

// Generate Unique ID

const generateUniqueId = () => {
  return Math.floor(Math.random() * 100000);
};

const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id,
  );
  saveTransactionToLocalStorage();
  toast.success("Transaction Deleted");
};
</script>

<style scoped></style>
