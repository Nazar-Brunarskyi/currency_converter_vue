<script lang="ts">
import { defineComponent } from 'vue';
import { getMoney } from './API/getData'
import type { Rates } from './types/ratesType'

interface DataProps {
  rates: Rates | null;
  currentRateOfCurrencyToConvert: number,
  currentRateOfConvertedCurrency: number,
  currencyToConvert: string,
  convertedCurrency: string,
  amountOfCurrencyToConvert: number,
  amountOfConvertedCurrency: number,
  conversionLimit: number,
}

export default defineComponent({
  data(): DataProps { 
    return {
      rates: null,
      currencyToConvert: 'UAH',
      convertedCurrency: 'USD',
      currentRateOfCurrencyToConvert: 0,
      currentRateOfConvertedCurrency : 0,
      amountOfCurrencyToConvert: 0,
      amountOfConvertedCurrency: 0,
      conversionLimit: 10000
    };
  },

  async mounted() {
    let cachedExchangeRates = JSON.parse(localStorage.getItem('exchangeRates') || 'null')

    if (!cachedExchangeRates) {
      const ratesFromServer = await getMoney();
      
      localStorage.setItem('exchangeRates', JSON.stringify(ratesFromServer))

      cachedExchangeRates = ratesFromServer;
    }

    this.currentRateOfCurrencyToConvert = cachedExchangeRates[this.currencyToConvert]
    this.currentRateOfConvertedCurrency = cachedExchangeRates[this.convertedCurrency]

    this.rates = cachedExchangeRates
  },

  watch: {
    currencyToConvert() {
      if (this.rates) {
        this.currentRateOfCurrencyToConvert = +this.rates[this.currencyToConvert]
        this.amountOfConvertedCurrency = +this.newAmountConvertedCurrency;

        if (this.isLimitReached) {
          this.calculateTheBiggestAmount();
        }
      }
    },

    convertedCurrency() {
      if (this.rates) {
        this.currentRateOfConvertedCurrency = +this.rates[this.convertedCurrency]
        this.amountOfConvertedCurrency = +this.newAmountConvertedCurrency

        if (this.isLimitReached) {
          this.calculateTheBiggestAmount();
        }
      }
    },

    amountOfCurrencyToConvert() {
      if (!this.rates){
        return;
      }
      
      if (this.isLimitReached) {
        this.calculateTheBiggestAmount();

        return;
      }

      if (this.$refs['outcoming__currency'] !== document.activeElement) {
        this.amountOfConvertedCurrency = this.newAmountConvertedCurrency 
      }
    },

    amountOfConvertedCurrency() {
      if (this.$refs['incoming__currency'] !== document.activeElement) { 
        this.amountOfCurrencyToConvert = this.newAmountOfConvertedCurrency
      }
    },
  },

  computed: {
    currenciesArr() {
      const allCurrenciesNames: string[] = [];

      for (const nameOfCurrency in this.rates) {
        allCurrenciesNames.push(nameOfCurrency)
      }

      return allCurrenciesNames.sort((a, b) => a.localeCompare(b));
    },

    newAmountConvertedCurrency() {
      return this.amountOfCurrencyToConvert
        / this.currentRateOfCurrencyToConvert
        * this.currentRateOfConvertedCurrency
    },

    newAmountOfConvertedCurrency() {
      return this.amountOfConvertedCurrency
        / (this.currentRateOfConvertedCurrency / this.currentRateOfCurrencyToConvert)
    },

    isLimitReached() {
      if (this.rates) {
        return this.amountOfCurrencyToConvert
          / +this.rates[this.currencyToConvert] > this.conversionLimit;
      }

      return false;
    }
  },

  methods: {
    calculateTheBiggestAmount() {
      if (this.rates) {
        this.amountOfCurrencyToConvert = +(this.conversionLimit * +this.rates[this.currencyToConvert])
          .toFixed(2);

        this.amountOfConvertedCurrency = +(this.conversionLimit * +this.rates[this.convertedCurrency])
          .toFixed(2);
      }
    },
  }
});

</script>

<template>
  <h1 class="title">Currency converter</h1>
  <form class="form">
    <label for="from_currency" class="form__label">currency to conversion:</label>
    <select 
      id="from_currency" 
      name="from_currency"
      class="form__selector"
      v-model="currencyToConvert"
    >
      <option 
        v-for="name in currenciesArr" 
        :key="name"
        :value="name"
        :selected="name === 'USD'"
      >
      {{ name }}
      </option>
    </select>

    <label for="to_currency" class="form__label">converted currency:</label>
    <select 
      id="to_currency" 
      name="to_currency" 
      class="form__selector"
      v-model="convertedCurrency"

    >
      <option 
        v-for="name in currenciesArr" 
        :key="name"
        :value="name"
        :selected="name === 'BTC'"
      >
      {{ name }}
      </option>
    </select>

    <label 
      for="from_amount"
      class="form__label"
    >
      Amount of incoming currency:
    </label>
    <input 
      ref="incoming__currency"
      type="number" 
      class="form__input"
      id="from_amount" 
      name="from_amount" 
      min="0"
      step="0.1"
      v-model="amountOfCurrencyToConvert"
      required
    >

    <label 
      for="to_amount"
      class="form__label"
    >
      Amount of converted currency:
    </label>
    <input 
      ref="outcoming__currency"
      type="number"
      class="form__input"
      id="to_amount"
      name="to_amount"
      min="0"
      step="0.01"
      v-model="amountOfConvertedCurrency"
      required
    >
  </form>
</template>

<style scoped></style>
