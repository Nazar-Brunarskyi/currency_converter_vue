<script lang="ts">
import type { Rates } from '@/types/ratesType';
import { defineComponent, type PropType } from 'vue';

interface State {
  selectedCurrency: string,
  defaultCurrencies: string[];
  defaultCurrenciesRates: string[];
}

export default defineComponent({
  props: {
    rates: {
      type: Object as PropType<Rates | null>,
      required: true,
    }
  },

  data(): State {
    return {
      selectedCurrency: 'USD',
      defaultCurrencies: ['USD', 'EUR', 'UAH'],
      defaultCurrenciesRates: ['USD', 'EUR', 'UAH', 'BTC', 'ETH']
    };
  },

  methods: {
    getConvertedPrice(price: number) {
      if (this.rates) {
        return 1 / (price / +this.rates[this.selectedCurrency]);
      }
    }
  },

  computed: {
    currenciesNamesArr() {
      const allCurrenciesNames: string[] = [];

      for (const nameOfCurrency in this.rates) {
        allCurrenciesNames.push(nameOfCurrency)
      }

      return allCurrenciesNames.sort((a, b) => a.localeCompare(b));
    },
  },
})
</script>

<template>
  <h1 class="title">Currency rates</h1>

  <form class="form">

    <label for="from_currency" class="form__label">
      currency:
    </label>

    <select v-model="selectedCurrency" id="from_currency" name="from_currency" class="form__selector">
      <option v-for="name in defaultCurrencies" :key="name" :selected="name === selectedCurrency">
        {{ name }}
      </option>
    </select>

    <table class="table">
      <tr class="table__row">
        <th class="table__head">Currency Name</th>
        <th class="table__head">The rate in the selected currency</th>
      </tr>

      <template 
        v-for="currency in defaultCurrenciesRates" 
        :key="currency"
      >
        <tr class="table__row" v-if="currency !== selectedCurrency">
          <td class="table__colum">{{ currency }}</td>
          <td class="table__colum">{{ rates ? getConvertedPrice(+rates[currency]) : 'error' }}</td>
        </tr>
      </template>
    </table>
  </form>
</template>