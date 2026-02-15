// Компонент для отображения информации о валюте (название, курс, динамика)

<script setup>
  import { defineProps, defineEmits, computed } from 'vue';

  const props = defineProps({
    currency: Object,
    currencyCode: String
  });

  const emit = defineEmits();

  const rateChange = computed(() => {
    const previousRate = props.currency.Previous || 0;
    const change = props.currency.Value - previousRate;
    return `${change > 0 ? '+' : '-'}${Math.abs(change).toFixed(2)} руб.`;
  });

  const rateClass = computed(() => {
    const change = props.currency.Value - (props.currency.Previous || 0);
    return change > 0 ? 'Positive' : 'Negative';
  });

  const selectCurrency = () => {
    // передача пользовательского события (через дочерний компонент) в родительский
    emit('select-currency', props.currency);
  };
</script>

<template>
  <div class="currency-card" @click="selectCurrency">
    <h3>{{ props.currency.Name }}</h3>
    <p>Курс: {{ props.currency.Value }} руб.</p>
    <p :class="rateClass">Динамика: {{ rateChange }}</p>
  </div>
</template>

<style scoped>
  .currency-card h3 {
    margin: 0;
  }

  .currency-card p {
    margin: 5px 0;
  }

  .currency-card .Positive {
    color: green;
  }

  .currency-card .Negative {
    color: red;
  }
</style>
