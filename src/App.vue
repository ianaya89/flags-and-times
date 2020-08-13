<template>
  <div id="app" >
    <header class="p-4 bg-green-400">
      <h1 class="text-6xl">
        <span role="img">🎏 🕐</span> Flags & Times
      </h1>
    </header>

    <main class="flex">
      <CountryList @change="convertTimes" />

      <section class="flex-1 p-5">
        <label for="selectedDate">
          <input id="selectedDate" type="datetime-local" v-model="state.selectedDate" placeholder="Seleccionar Fecha">
        </label>
        <div v-html="state.message" class="text-3xl">
        </div>
      </section>
    </main>
  </div>
</template>

<script>
import { reactive, watch } from 'vue'
import { utcToZonedTime } from 'date-fns-tz'
import emojis from '@/emojis'

import CountryList from '@/components/CountryList'

export default {
  name: 'App',

  components: { CountryList },

  setup () {
    const state = reactive({
      convertedCountries: [],
      selectedDate: new Date(),
      message: ''
    })

    let localCountries = []

    function convertTimes (countries) {
      if (!countries.length) {
        return
      }

      localCountries = countries
      let message = ''
      const now = state.selectedDate

      countries.forEach(c => {
        const dates = Array.from(new Set(c.timezones.map(
          tz => utcToZonedTime(now, tz).toLocaleString())))

        let flags = emojis['flag-' + c.id.toLowerCase()].b
          .split('-')
          .map(code => `&#x${code};`)
          .join('')

        flags = `<span role="img">${flags}</span>`

        message += `
            ${c.name}:
            ${flags}
            ${dates.join('\n')}
            <br><br>
          `
      })

      state.message = message
    }

    watch(
      () => state.selectedDate,
      () => {
        convertTimes(localCountries)
      }
    )

    return { state, convertTimes }
  }
}
</script>
