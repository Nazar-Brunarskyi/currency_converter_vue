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
}

export default defineComponent({
  data(): DataProps { 
    return {
      rates: null,
      currencyToConvert: 'USD',
      convertedCurrency: 'BTC',
      currentRateOfCurrencyToConvert: 0,
      currentRateOfConvertedCurrency : 0,
      amountOfCurrencyToConvert: 0,
      amountOfConvertedCurrency: 0,
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
      }
    },

    convertedCurrency() {
      if (this.rates) {
        this.currentRateOfConvertedCurrency = +this.rates[this.convertedCurrency]
        this.amountOfConvertedCurrency = +this.newAmountConvertedCurrency
      }
    },

    amountOfCurrencyToConvert() {
      // if (this.amountOfCurrencyToConvert * this.currentRateOfCurrencyToConvert > 10000 ) {
      //   this.amountOfCurrencyToConvert = 10000 * this.currentRateOfCurrencyToConvert
      //   console.log(this.currentRateOfCurrencyToConvert);
      // }
      this.amountOfConvertedCurrency = +this.newAmountConvertedCurrency;
    },

    amountOfConvertedCurrency() {
      this.amountOfCurrencyToConvert = +this.newAmountOfConvertedCurrency
    },
  },

  computed: {
    currenciesArr() {
      const allCurrenciesNames: string[] = [];

      for (const nameOfCurrency in this.rates) {
        allCurrenciesNames.push(nameOfCurrency)
      }

      return allCurrenciesNames;
    },

    newAmountConvertedCurrency (){
      return this.amountOfCurrencyToConvert 
        / this.currentRateOfCurrencyToConvert
        * this.currentRateOfConvertedCurrency
    },
    newAmountOfConvertedCurrency () {
      return this.amountOfConvertedCurrency 
        / (this.currentRateOfConvertedCurrency / this.currentRateOfCurrencyToConvert)
    }
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
      type="number"
      class="form__input"
      id="to_amount"
      name="to_amount"
      min="0"
      step="0.01"
      v-model="amountOfConvertedCurrency"
      required
    >

    <!-- <input type="submit" value="Конвертувати"> -->
  </form>
</template>

<style scoped></style>
