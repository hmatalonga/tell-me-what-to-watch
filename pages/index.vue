<template>
  <div
    class="flex items-center justify-center h-screen"
    :style="`background: linear-gradient(to right, rgba(31.5, 31.5, 31.5, 1) 150px, rgba(31.5, 31.5, 31.5, 0.84) 100%), url(https://image.tmdb.org/t/p/original/${show.backdrop_path}) no-repeat scroll 50%/cover transparent;`"
  >
    <div class="max-w-7xl mx-auto">
      <div class="grid grid-cols-3 gap-4">
        <div class="col-span-1 relative">
          <img
            class="max-w-sm h-auto rounded-lg"
            :src="`https://image.tmdb.org/t/p/w500/${show.poster_path}`"
            :alt="show.name"
          />
          <div class="inline-flex absolute -bottom-2 -right-2 font-title font-bold text-4xl w-24 h-24 rounded-full" :class="rating">
            <div class="w-full flex items-center justify-center text-center">{{ show.vote_average }}</div>
          </div>
        </div>
        <div class="col-span-2 flex items-center">
          <div class="flex flex-col space-y-12 px-6">
            <div class="header">
              <div class="font-title font-bold text-5xl text-white mb-2">
                {{ show.name }}
                <span class="text-gray-300">({{ latestYear }})</span>
              </div>
              <div class="text-slate-200 flex space-x-6">
                <span>{{ genres }}</span>
                <span>{{ show.episode_run_time[0] }}m</span>
              </div>
              <div class="font-title text-xl text-slate-200 italic mt-6">{{ show.tagline }}</div>
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
  async asyncData({ $axios, $config: { apiSecret } }) {
    const series = database.map(e => e.id)
    const id = series[Math.floor(Math.random() * series.length)]

    const show = await $axios.$get(
      `https://api.themoviedb.org/3/tv/${id}?api_key=${apiSecret}`
    )

    return { show }
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
