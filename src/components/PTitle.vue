<script setup lang="ts">
import { useQuery } from '@tanstack/vue-query'
import { useRouter } from 'vue-router'
import { computed } from 'vue'
import axios from 'axios'
import type { PokemeonType } from '@/@data/data'

const router = useRouter()
const handleText = (text: Event) => {
  const params = text.target?.value
  router.replace({ query: { type: `${params}` } })
}
const titleSelect = 'Select pokemon\'s type:'

const data = useQuery({
  queryKey: ['filter'], queryFn: () => {
    return axios.get(import.meta.env.VITE_APP_URL_POKEMON_TYPE).then((data: any) => data.data)
  }
})
const getData = computed<PokemeonType[]>(() => data.data.value?.data)
</script>

<template>
  <div class="greetings">
    <div class="search">
      <label for="type">{{titleSelect}}</label>
      <select name="pokemon" @change="handleText" id="type">
        <option :value="type.id" v-for="(type, id) in getData" :key="id">{{ type.name }}</option>
      </select>
    </div>
  </div>
</template>

<style scoped>
h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings {
  display: flex;
  justify-content: center;

  h1, h3 {
    text-align: center;
  }

  .search {
    select {
      width: 150px;
      height: 30px;
      margin: 0 8px;
    }
  }
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}
</style>
