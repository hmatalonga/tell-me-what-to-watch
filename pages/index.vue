<template>
  <div
    class="flex items-center justify-center sm:h-screen"
    :style="`background: linear-gradient(to right, rgba(31.5, 31.5, 31.5, 1) 150px, rgba(31.5, 31.5, 31.5, 0.84) 100%), url(https://image.tmdb.org/t/p/original/${show.backdrop_path}) no-repeat scroll 50%/cover transparent;`"
  >
    <div class="max-w-7xl mx-auto py-12">
      <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
        <div class="col-span-1 relative px-8 mx-auto">
          <div class="max-w-sm flex items-center">
            <img
              class="w-full h-auto rounded-lg"
              :src="`https://image.tmdb.org/t/p/w500/${show.poster_path}`"
              :alt="show.name"
            />
            <div
              class="inline-flex absolute -bottom-2 right-2 font-title font-bold text-2xl w-16 h-16 sm:w-24 sm:h-24 sm:text-4xl rounded-full"
              :class="rating"
            >
              <div class="w-full flex items-center justify-center text-center">
                {{ show.vote_average }}
              </div>
            </div>
          </div>
        </div>
        <div class="col-span-1 sm:col-span-2 px-8">
          <div class="flex flex-col space-y-12 py-6">
            <div class="action mb-4 mx-auto sm:mx-0">
              <button
                type="button"
                @click="fetchShow()"
                class="inline-flex items-center px-6 py-3 border border-transparent shadow-sm text-base font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
              >
                <svg
                  class="-ml-1 mr-3 h-5 w-5"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                  xmlns="http://www.w3.org/2000/svg"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="2"
                    d="M12 8v13m0-13V6a2 2 0 112 2h-2zm0 0V5.5A2.5 2.5 0 109.5 8H12zm-7 4h14M5 12a2 2 0 110-4h14a2 2 0 110 4M5 12v7a2 2 0 002 2h10a2 2 0 002-2v-7"
                  ></path>
                </svg>
                Pick Something
              </button>
            </div>
            <div class="header">
              <div
                class="font-title font-bold text-3xl sm:text-5xl text-white mb-2"
              >
                {{ show.name }}
                <span class="text-gray-300">({{ latestYear }})</span>
              </div>
              <div class="text-slate-200 flex space-x-6">
                <span>{{ genres }}</span>
                <span>{{ show.episode_run_time[0] }}m</span>
              </div>
              <div class="font-title text-xl text-slate-200 italic mt-6">
                {{ show.tagline }}
              </div>
            </div>
            <div class="overview">
              <div class="font-title font-bold text-2xl text-white mb-2">
                Overview
              </div>
              <div class="text-lg text-slate-200">{{ show.overview }}</div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { database } from '~/static/data.json'

export default {
  name: 'IndexPage',
  data() {
    return {
      series: database.map((e) => e.id).sort((a, b) => 0.5 - Math.random()),
    }
  },
  async asyncData({ $axios, $config: { apiSecret } }) {
    const series = database.map((e) => e.id).sort((a, b) => 0.5 - Math.random())
    const id = series.pop()

    const show = await $axios.$get(
      `https://api.themoviedb.org/3/tv/${id}?api_key=${apiSecret}`
    )
    return { show }
  },
  methods: {
    async fetchShow() {
      if (this.series.length == 0) {
        this.series = database
          .map((e) => e.id)
          .sort((a, b) => 0.5 - Math.random())
      }

      const id = this.series.pop()

      const show = await this.$axios.$get(
        `https://api.themoviedb.org/3/tv/${id}?api_key=${this.$config.apiSecret}`
      )
      this.show = show
    },
  },
  computed: {
    latestYear() {
      return this.show.last_air_date.slice(0, 4)
    },
    genres() {
      return this.show.genres.map((g) => g.name).join(', ')
    },
    rating() {
      if (this.show.vote_average >= 7) {
        return 'bg-green-600 text-green-100'
      } else if (this.show.vote_average >= 6) {
        return 'bg-yellow-600 text-yellow-100'
      }
      return 'bg-red-600 text-red-100'
    },
  },
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Merriweather:wght@400;700&display=swap');

.font-title {
  font-family: 'Merriweather', sans-serif;
}
</style>
