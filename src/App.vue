<script lang="ts">
import { defineComponent } from 'vue';
import { getMoney } from './API/getData'
import CurrencyRates from './components/Currency-rates.vue'
import CurrencyConverter from './components/Currency-converter.vue'
import type { Rates } from './types/ratesType'

interface State {
  rates: Rates | null;
}

export default defineComponent({
  components: {
    CurrencyRates,
    CurrencyConverter,
},

  data(): State { 
    return {
      rates: null,
    };
  },

  async mounted() {
    let cachedExchangeRates = JSON.parse(localStorage.getItem('exchangeRates') || 'null')

    if (!cachedExchangeRates) {
      const ratesFromServer = await getMoney();
      
      localStorage.setItem('exchangeRates', JSON.stringify(ratesFromServer))

      cachedExchangeRates = ratesFromServer;
    }

    this.rates = cachedExchangeRates
  },

  methods: {
    updateRates(newRates: Rates) {
      this.rates = newRates;
      localStorage.setItem('exchangeRates', JSON.stringify(newRates))
    }
  }
});

</script>

<template>
  <CurrencyConverter :rates="rates" v-if="rates"/>

  <CurrencyRates 
    :rates="rates"
    @update-rates="updateRates"
  />

  <!-- <popUpVue :message="'23123'" /> -->
</template>