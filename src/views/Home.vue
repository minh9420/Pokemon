<script setup lang="ts">
import { computed, ref, watch } from 'vue'
import { type Pokemeon } from '@/@data/data'
import { useQuery } from '@tanstack/vue-query'
import axios from 'axios'
import PTitle from '@/components/PTitle.vue'
import { useRoute } from 'vue-router'
import Modal from '@/components/Modal.vue'

const route = useRoute()
const type = computed(() => route.query.type)
const sortType = ref<string>()
const title = 'LIST OF POKEMON'
const stateModal = ref(false)
const idParams = ref()

const params = computed(() => {
  return {
    filter: {
      type: type.value
    },
    sort: sortType.value
  }
})

const handleSort = (type: string) => {
  sortType.value = sortType.value === type ? undefined : type
}


const data = useQuery({
  queryKey: ['get'], queryFn: () => {
    return axios.get(import.meta.env.VITE_APP_URL_POKEMON, { params: params.value }).then((data: any) => data.data)
  }
})

const conditionSort = (key: string) => {
  return (key === 'total' || key === 'hp' || key === 'attack' || key === 'defense' || key === 'sp_atk' || key === 'sp_def' || key === 'speed')
}


const getData = computed<Pokemeon[]>(() => data.data.value?.data)
watch(params, (newValue) => {
    console.log(newValue)
    data.refetch()
  },
  { immediate: true })
</script>
<template>
  <div class="home">
    <div class="loader" v-if="!data.isLoading">
    </div>
    <div v-else>
      <div class="home__header">
        <h1>{{ title }}</h1>
      </div>
      <PTitle class="home__title" />
      <div class="home__body">
        <table style="width:100%">
          <thead>
          <tr v-if="getData">
            <th v-for="(key, id) in Object.keys(getData[0])" :key="id">
              <div class="home__body__content" @click="() => { handleSort(key) }">
                <template v-if="conditionSort(key)">
                <span class="text">
                  {{ key.toUpperCase() }}
                </span>
                  <i v-if="sortType === key" class="fa fa-sort-desc" style="margin: 0 2px" aria-hidden="true"></i>
                </template>
                <template v-else>
                <span class="text">
                {{ key.toUpperCase() }}
                </span>
                </template>
              </div>
            </th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(item, id) in getData" :key="id">
            <td v-for="key in Object.keys(getData[0])" :key="key"  @click="stateModal = !stateModal; idParams = item.id">
              <template v-if="key === 'created_at' || key === 'updated_at'">
                {{ (new Date(item[key])).toLocaleDateString() }}
              </template>
              <template v-else>
                <span class="text_color">
                  {{ item && item[key] }}
                </span>
              </template>

            </td>
          </tr>
          </tbody>
        </table>
      </div>
      <Modal :open="stateModal" :params="idParams || ''" @close="stateModal = false"/>
    </div>
  </div>
</template>

<style lang="scss">
.home {
  width: 100%;

  .loader {
    border: 16px solid #f3f3f3; /* Light grey */
    border-top: 16px solid #3498db; /* Blue */
    border-radius: 50%;
    width: 120px;
    height: 120px;
    animation: spin 2s linear infinite;
  }

  @keyframes spin {
    0% {
      transform: rotate(0deg);
    }
    100% {
      transform: rotate(360deg);
    }
  }

  &__header {
    h1 {
      background-color: #3498db;
      padding: 0 10px;
      font-weight: bold;
    }
    @media (max-width: 1024px) {
      font-size: 0.8rem;
    }
  }

  &__title {
    padding: 1.5rem 0;
  }

  &__body {
    max-width: 1280px;
    margin: 0 auto;
    overflow-x: scroll;

    table, th, td {
      border-collapse: collapse;
      padding: 3px;
    }

    th {
      border: 1px solid white;
      background-color: #96D4D4;
      font-size: 1rem;
      font-weight: 500;
      min-width: 100px;

      &:nth-child(1) {
        min-width: 300px;
      }

      &:nth-child(3) {
        min-width: 150px;
      }

      &:nth-child(13) {
        min-width: 150px;
      }

      &:nth-child(14) {
        min-width: 150px;
      }

      &:nth-child(15) {
        min-width: 150px;
      }

      &:nth-child(16) {
        min-width: 150px;
      }
    }

    td {
      border-bottom: 1px solid #D6EEEE;

      a:hover {
        background-color: transparent !important;
      }
    }

    .text_color {
      color: black;
    }

    tr:hover {
      cursor: pointer;
      background-color: #D6EEEE;
    }

    &__content {
      display: flex;
      justify-content: center;

      .text {
        font-size: 1rem;
        font-weight: 500;
      }
    }
    @media (max-width: 1024px) {
      th {
        font-size: 14px;
        font-weight: initial;
        min-width: 50px;

        &:nth-child(1) {
          min-width: 50px;
        }

        &:nth-child(3) {
          min-width: 40px;
        }

        &:nth-child(13) {
          min-width: 40px;
        }

        &:nth-child(14) {
          min-width: 40px;
        }

        &:nth-child(15) {
          min-width: 40px;
        }

        &:nth-child(16) {
          min-width: 40px;
        }
      }
      .text_color {
        font-size: 12px;
      }
      &__content {
        display: flex;
        justify-content: center;

        .text {
          font-size: 12px;
          font-weight: 500;
        }
      }
    }

  }
}
</style>