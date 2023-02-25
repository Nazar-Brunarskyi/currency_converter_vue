<script lang="ts">
import { defineComponent, type PropType } from 'vue';

interface State {
  selectedCurrency: string,
}

export default defineComponent({
  name: 'addCurrency',

  props: {
    availableCurrencies: {
      type: Array as PropType<string[]>,
      required: true,
    },

    addedCurrencies: {
      type: Array as PropType<string[]>,
      required: true,
    }
  },

  emits: ["addCurrency", 'hideComponent'],

  data(): State {
    return {
      selectedCurrency: '',
    };
  },

  methods: {
    updateCurrenciesList() {
      if (this.selectedCurrency) {
        this.$emit('addCurrency', this.selectedCurrency);
      }

      this.selectedCurrency = '';
    }
  }
})
</script>

<template>
  <div class="blur-blok">ddsf</div>
  <div class="form pop-up">
    <form>
      <label for="from_currency" class="form__label">
        Currencies:
      </label>

      <select v-model="selectedCurrency" id="from_currency" name="from_currency" class="form__selector">
        <option value="" disabled>choose currency to add</option>

        <template v-for="name in availableCurrencies" :key="name">
          <option :selected="name === selectedCurrency" v-if="!addedCurrencies.includes(name)">
            {{ name }}
          </option>
        </template>
      </select>

      <div class="flex-container flex-container--between">
        <button 
          class="button" 
          @click.prevent="updateCurrenciesList"
        >
          add to list
        </button>

        <button 
          class="button"
          @click.prevent="$emit('hideComponent')"
        >
          close
        </button>
      </div>
    </form>
  </div>
</template>