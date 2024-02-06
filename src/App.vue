

<template>
  
  <Header/>
  <div class="container">
    <Balance :total="total"/>
    <IncomeExpense :income="+income" :expenses="+expenses"/>
    <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
  </div>
</template>

<script setup>
  import Header from './components/Header.vue';
  import Balance from './components/Balance.vue';
  import IncomeExpense from './components/IncomeExpense.vue';
  import TransactionList from './components/TransactionList.vue'
  import AddTransaction from './components/AddTransaction.vue';
  import { useToast } from 'vue-toastification';
  import {ref , computed, onMounted} from 'vue';
  const toast = useToast()
  const transactions = ref([]);

  const total = computed(()=>{
    return transactions.value.reduce((acc,transaction)=>{
      return acc+transaction.amount
    },0)
  })

  onMounted(()=>{
    const savedTransactions = JSON.parse(localStorage.getItem('transactions'));
    if(savedTransactions){
      transactions.value = savedTransactions;
    }
  })


  const income = computed(()=>{
    return transactions.value.filter(
      (transaction)=>transaction.amount>0).reduce((acc,transaction)=>{
      return acc+transaction.amount
    },0).toFixed(2)
  })


  const expenses = computed(()=>{
    return transactions.value.filter(
      (transaction)=>transaction.amount<0).reduce((acc,transaction)=>{
      return acc+transaction.amount
    },0).toFixed(2)
  })

  const handleTransactionSubmitted = (transactionData)=>{
    transactions.value.push({
      id:generatedUniqueId(),
      text:transactionData.text,
      amount:transactionData.amount
    })
    saveTransactionsToLocalStorage()
    toast.success("Transaction completed")
  }

  const generatedUniqueId = () =>{
    return Math.floor(Math.random()*10000000000)
  }

  const handleTransactionDeleted = (id) =>{
    transactions.value = transactions.value.filter(
      (transaction) => transaction.id !== id
    )
    saveTransactionsToLocalStorage()
    toast.success('Transaction deleted')
  }

  const saveTransactionsToLocalStorage = () =>{
    localStorage.setItem('transactions',JSON.stringify(transactions.value))
  } 


</script>

