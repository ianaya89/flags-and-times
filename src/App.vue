<template>
  <div id="app" >
    <header class="p-4 bg-green-400">
      <h1 class="text-6xl">
        <span role="img">🎏 🕐</span> Flags & Times
      </h1>
    </header>

    <main>
      <h2>Seleccionar Paises</h2>
      <input v-model="state.filter" aria-label="Filtrar Paises" placeholder="Filtrar Paises">
      <ul>
        <li v-for="c in filteredCountries" :key="c.id" class="text-3xl">
          <input type="checkbox" :id="c.id">
          <label :for="c.id">{{ c.name }} {{ c.timezones }}</label>
        </li>
      </ul>
    </main>
  </div>
</template>

<script>
import { reactive, computed } from 'vue'
import ct from 'countries-and-timezones'

export default {
  name: 'App',

  setup () {
    const state = reactive({
      countries: [],
      filter: ''
    })

    state.countries = Object.values(ct.getAllCountries())

    const filteredCountries = computed(() => {
      if (!state.filter) {
        return state.countries
      }

      return state.countries
        .filter(c => {
          console.log(c.name.toLowerCase(), state.filter.toLowerCase(),
            c.name.toLowerCase().includes(state.filter.toLowerCase()))
          return c.name.toLowerCase().includes(state.filter.toLowerCase())
        })
    })

    return { state, filteredCountries }
  }
}
</script>
