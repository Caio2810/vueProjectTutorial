<!--ARRUMAR BALANÇO, MOSTRAR APENAS .00 -->

<template>
    <Header />
    <div class="container">
        <Balance :total="total"/>
        <IncomeExpensives :income="+income" :expenses="+expenses" />
        <TransactionList :transactions="transactions" 
        @transactionDeleted="handleTransactionDeleted" />
        <AddTransaction 
        @transactionSubmitted="handleTransactionSubmitted" />
    </div>
</template>

<script setup>
    import Header from './components/Header.vue'
    import Balance from './components/Balance.vue'
    import IncomeExpensives from './components/IncomeExpensives.vue'
    import TransactionList from './components/TransactionList.vue'
    import AddTransaction from './components/AddTransaction.vue'

    import { useToast } from 'vue-toastification'

    import { ref, computed, onMounted } from 'vue';

    const toast = useToast();

    const transactions = ref([]);

    onMounted(() => {
        const savedTransactions = JSON.parse(localStorage.getItem
        ('transactions'))

        if(savedTransactions) {
            transactions.value = savedTransactions;
        }
    })

    const total = computed(() => {
        return transactions.value.reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0);
    });

    const income = computed(() => {
        return transactions.value
        .filter((transaction) => transaction.amount > 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0).toFixed(2);
    });

    const expenses = computed(() => {
        return transactions.value
        .filter((transaction) => transaction.amount < 0)
        .reduce((acc, transaction) => {
            return acc + transaction.amount;
        }, 0).toFixed(2);
    });

    const { emit } = defineEmits(['transactionSubmitted']);

    const handleTransactionSubmitted = (transactionData) => {
        transactions.value.push({
            id: generateUniqueId(),
            text: transactionData.text,
            amount: transactionData.amount
        })

        saveTransactionsToLocalStorage()

        toast.success('Adicionado com sucesso!');
    };

    //generateUniqueId()
    const generateUniqueId = () =>{
        return Math.floor(Math.random() * 1000000);
    }

    //deletar
    const handleTransactionDeleted = (id) => {
        transactions.value = transactions.value.filter((transaction) =>
        transaction.id !== id);

        saveTransactionsToLocalStorage()
        
        toast.success('Deletado com sucesso!')
    };

    //localstorage
    const saveTransactionsToLocalStorage = () => {
        localStorage.setItem('transactions', JSON.stringify(transactions.value));
    }
</script>
