<template>
  <div id="app" >
    <header class="p-4 bg-green-400">
      <h1 class="text-6xl">
        <span role="img">🎏 🕐</span> Flags & Times
      </h1>
    </header>

    <main class="flex">
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

      <section class="flex-1 p-5">
        <ul>
          <li v-for="c in state.convertedCountries" :key="c.name" class="text-3xl text-green-400">
            {{ c.name }}: {{ c.dates }}
          </li>
        </ul>
        <label for="message">
        <textarea id="message" disabled :value="state.message">
        </textarea>
        </label>
      </section>
    </main>
  </div>
</template>

<script>
import { reactive, computed, watch } from 'vue'
import ct from 'countries-and-timezones'
import { utcToZonedTime } from 'date-fns-tz'

export default {
  name: 'App',

  setup () {
    const state = reactive({
      countries: [],
      convertedCountries: [],
      filter: '',
      message: ''
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
        const convertedCountries = []
        let message = ''
        const now = new Date()

        selectedCountries.value.forEach(c => {
          const dates = Array.from(new Set(c.timezones.map(tz => utcToZonedTime(now, tz).toLocaleString())))

          message += `
            ${c.name}:
            ${dates.join('\n')}
          `
          convertedCountries.push({
            name: c.name,
            dates
          })
        })

        state.message = message
        state.convertedCountries = convertedCountries
      }
    )

    return { state, filteredCountries, selectedCountries }
  }
}
</script>
