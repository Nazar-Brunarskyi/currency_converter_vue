<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({

})
</script>

<template>
  <h1 class="title">Currency rates</h1>

  <form class="form">

    <label for="from_currency" class="form__label">currency to conversion:</label>
    <select id="from_currency" name="from_currency" class="form__selector">
      <option>123123</option>
    </select>

    <table class="table">
      <tr class="table__row">
        <th class="table__head">Колонка 1</th>
        <th class="table__head">Колонка 2</th>
      </tr>
      <tr class="table__row">
        <td class="table__colum">Рядок 1, Колонка 1</td>
        <td class="table__colum">Рядок 1, Колонка 2</td>
      </tr>
      <tr class="table__row">
        <td class="table__colum">Рядок 2, Колонка 1</td>
        <td class="table__colum">Рядок 2, Колонка 2</td>
      </tr>
    </table>
  </form>
</template>