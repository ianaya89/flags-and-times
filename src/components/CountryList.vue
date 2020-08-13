<template>
  <section class="flex-1 p-5">
    <input v-model="state.filter" aria-label="Filtrar Paises" placeholder="Filtrar Paises" class="bg-white focus:outline-none focus:shadow-outline border border-gray-300 rounded-lg py-2 px-4 block appearance-none leading-normal">
    <ul>
      <li v-for="c in filteredCountries" :key="c.id" class="text-3xl">
        <label :for="c.id">
          <input v-model="c.selected" :value="c.selected" type="checkbox" :id="c.id">
          {{ c.name }}
        </label>
      </li>
    </ul>
  </section>
</template>

<script>
import ct from 'countries-and-timezones'
import { reactive, computed, watch } from 'vue'

export default {
  name: 'CountryList',

  setup (props, context) {
    const state = reactive({
      countries: [],
      filter: ''
    })

    state.countries = Object.values(ct.getAllCountries())
      .map(c => ({ ...c, selected: false }))

    const filteredCountries = computed(() => {
      if (!state.filter) {
        return state.countries
      }

      return state.countries
        .filter(c => c.name.toLowerCase().includes(state.filter.toLowerCase()))
    })

    const selectedCountries = computed(() => {
      return state.countries.filter(c => c.selected)
    })

    watch(
      selectedCountries,
      () => {
        context.emit('change', selectedCountries.value)
      }
    )

    return {
      state, filteredCountries
    }
  }
}
</script>
