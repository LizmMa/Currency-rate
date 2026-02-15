// Главный (родительский) компонент

<script setup>
  import { ref, onMounted, computed } from 'vue';
  // добавляем дочерние компоненты - связывание 
  import CurrencyCard from './components/CurrencyCard.vue';
  import CurrencyConverter from './components/CurrencyConverter.vue';


  import 'bootstrap/dist/css/bootstrap.min.css';

  const currencies = ref({});
  const selectedCurrency = ref(null);
  const searchQuery = ref("");

  const fetchCurrencies = async () => {
    try {
      const response = await fetch(`https://www.cbr-xml-daily.ru/daily_json.js`);
      const data = await response.json();
      currencies.value = data.Valute;
    } catch (error) {
      console.error('Ошибка при загрузке данных:', error);
    }
  };

  const selectCurrency = (currency) => {
    selectedCurrency.value = currency;
  };
  
  const closeConverter = () => {
    selectedCurrency.value = null;
  };

  // Фильтрация валют по имени
  const filteredCurrencies = computed(() => {
    // Проходим по всем валютам и фильтруем их по имени, если оно соответствует поисковому запросу
    // используем Object.entries() для получения массива пар [ключ, значение], где ключ — это код валюты, 
    // а значение — объект с данными о валюте.
    return Object.entries(currencies.value).filter(([key, currency]) =>
      currency.Name.toLowerCase().includes(searchQuery.value.toLowerCase())
    );
  });

  onMounted(() => {
    fetchCurrencies();
  });
</script>

<template>
  <div id="app">
    <h1 class="text-center">Курсы Валют</h1>

    <!-- Поиск валюты -->
    <div class="d-flex justify-content-center w-100 mb-4">
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Поиск по названию валюты"
        class="form-control w-50"
      />
    </div>

    <div class="container">
      <!-- g-4 (отступы между колонками) -->
      <div class="row g-40 justify-content-center">
        <CurrencyCard 
          v-for="([key, currency]) in filteredCurrencies" 
          :key="key" 
          :currency="currency"
          :currency-code="key"
          @select-currency="selectCurrency"
          class="currency-card"
        />
      </div>
    </div>

    <!-- Окно для конвертации -->
    <div v-if="selectedCurrency" class="converter">
      <CurrencyConverter
        :currency="selectedCurrency"
        @close="closeConverter"
      />
    </div>
  </div>
</template>

<style>
  .converter {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    /* background: rgba(217, 241, 172, 0.825);  */
    background: rgba(172, 213, 241, 0.825);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .container {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
    padding: 0 15px;
  }

  #app {
    display: flex;
    flex-direction: column;
    justify-content: flex-start; /* Верхняя часть страницы */
    align-items: center;
    padding: 20px;
  }

  h1 {
    font-size: 2rem;
    color: #333;
  }

  .currency-card {
    background-color: #e0e9f1;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
    padding: 20px;
    border-radius: 10px;
    /* длинные названия не выходили за пределы карточки. 
    Эти стили гарантируют, 
    что текст будет обрезаться с многоточием, если он слишком длинный. */
    /* overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap; */
    margin-bottom: 10px;
    cursor: pointer;
    width: 300px;
  }

  /* Эффект наведения на карточки */
  .currency-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 8px 10px rgba(0, 0, 0, 0.1);
  }
</style>
