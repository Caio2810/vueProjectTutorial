<template>
          <h3>Inserir novo valor</h3>
      <form id="form" @submit.prevent="onSubmit">
        <div class="form-control">
          <label for="text">Nome</label>
          <input type="text" id="text" v-model="text" placeholder="Identificação..." />
        </div>
        <div class="form-control">
          <label for="amount"
            >Valor <br />
            (negativo = despesa  / positivo = renda)</label
          >
          <input type="text" id="amount" v-model="amount" placeholder="R$..." />
        </div>
        <button class="btn">Add transaction</button>
      </form>
</template>

<script setup>
import { ref, defineEmits } from 'vue';
import { useToast } from 'vue-toastification';

const toast = useToast()
const text = ref('');
const amount = ref('');
const emit = defineEmits(['transactionSubmitted'])

const onSubmit = () => {
    if(!text.value || !amount.value) {
        toast.error('Todos os campos devem ser preenchidos');
        return;
    }

    const transactionData = {
        text: text.value,
        amount: parseFloat(amount.value)
    }

    emit('transactionSubmitted', transactionData)

    text.value=''
    amount.value=''
}
</script>
