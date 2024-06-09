<script setup lang="ts">
import { useQuery } from '@tanstack/vue-query'
import axios from 'axios'
import { computed, ref } from 'vue'
import type { Pokemeon, PokemeonType } from '@/@data/data'

interface ModalProps {
  open: Boolean;
  params: string;
}

interface ModalEmits {
  (e: 'close'): void
}

const props = defineProps<ModalProps>()
const emits = defineEmits<ModalEmits>()
const myModalref = ref(null)
const state = computed(() => !!props.open)
const id = computed(() => props.params)
const data = useQuery({
  queryKey: ['detail'], queryFn: () => {
    return axios.get(`${import.meta.env.VITE_APP_URL_POKEMON_DETAIL}/${id.value}`).then((data: any) => data.data)
  }, enabled: state
})

const image = useQuery({
  queryKey: ['image'], queryFn: () => {
    return axios.get(`${import.meta.env.VITE_APP_URL_POKEMON_SPRITE}/${id.value}/sprite`, { responseType: 'blob' })
  }, enabled: state
})
const getData = computed<Pokemeon>(() => data.data.value?.data)
const showImag = () => {
  const url = URL.createObjectURL(image.data.value?.data)
  return url
}

const imageUrl = computed(() => {
  if (image.data.value) {
    return URL.createObjectURL(image.data.value?.data)
  }
})

const dataType = useQuery({
  queryKey: ['filter'], queryFn: () => {
    return axios.get(import.meta.env.VITE_APP_URL_POKEMON_TYPE).then((data: any) => data.data)
  }
})
const getDataType = computed<PokemeonType[]>(() => dataType.data.value?.data)

const handleType = (number: number) => {
  const [type] = getDataType.value.filter(x => x.id === number)
  return type.name
}

</script>

<template>
  <div id="myModal" class="modal" :class="props.open ? 'modal--open' : 'modal--close'" ref="myModalref"
       @click="(event) => {event.target === myModalref ? emits('close') : null}">
    <!-- Modal content -->
    <div class="modal__content" v-if="data.isLoading">
      <h2 class="title">POKEMON INFO</h2>
      <div class="infor">
        <div class="avata">
          <div>
            <h3 class="name">{{ getData && getData.name }}</h3>
          </div>
          <img class="image" :src="imageUrl" alt="API Image" v-if="image.isLoading">
        </div>
        <div class="detail">
          <button @click="emits('close')" class="close"><i class="fa fa-times" aria-hidden="true"></i></button>
          <div class="detail__content">
            <div class="key">Number:</div>
            <span class="val">{{ getData && getData.number }}</span>
          </div>
          <div class="detail__content">
            <div class="key">Type:</div>
            <span class="val">{{ getData && handleType(getData.type_1) }}</span>
          </div>
          <div class="detail__content">
            <div class="key">HP:</div>
            <span class="val">{{ getData && getData.hp }}</span>
          </div>
          <div class="detail__content">
            <div class="key">ATTACH:</div>
            <span class="val">{{ getData && getData.attack }}</span>
          </div>
          <div class="detail__content">
            <div class="key">DEFENSE:</div>
            <span class="val">{{ getData && getData.defense }}</span>
          </div>
          <div class="detail__content">
            <div class="key">SPEED:</div>
            <span class="val">{{ getData && getData.speed }}</span>
          </div>
<!--          <div class="detail__content">-->
<!--            <div class="key">GENERATION:</div>-->
<!--            <span class="val">{{ getData && getData.generation }}</span>-->
<!--          </div>-->
        </div>
      </div>

    </div>

  </div>
</template>

<style scoped lang="scss">

.modal {
  display: none; /* Hidden by default */
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  padding-top: 100px; /* Location of the box */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0, 0, 0); /* Fallback color */
  background-color: rgba(0, 0, 0, 0.4); /* Black w/ opacity */
  &--open {
    display: block;
  }

  &--close {
    display: none;
  }

  .modal__content {
    position: relative;
    background-color: #fefefe;
    //background-color: #96D4D4;
    margin: auto;
    //padding: 20px;
    border: 1px solid #888;
    width: 30%;
    z-index: 100;


    .title {
      background-color: #dbb17cd1;
      font-weight: bold;
      padding: 0 10px;
    }

    .infor {
      display: flex;
      gap: 25px;
      background-color: #ffeca5;
      //justify-content: space-between;
      .close {
        //display: block;
        //font-size: 2rem;
        position: absolute;
        top: 0.5rem;
        right: 0.5rem;
        //line-height: 0;
        //width: 3rem;
        &:hover {
          cursor: pointer
        }
      }

      .avata {
        background-color: #96d4d475;
        display: flex;
        flex-direction: column;

        .image {
          margin: 5px;
          width: 200px;
          background-color: #fefefe;
        }

        .name {
          //font-family: "Bodoni MT Condensed";
          font-weight: bold;
          margin: 0 5px;
        }
      }

      .detail {
        display: flex;
        flex-direction: column;
        justify-content: center;
        gap: 5px;
        flex-grow: 1;
        margin: 5px;
        &__content {

          display: flex;
          align-items: center;
          gap: 5px;

          .key, .val {
            font-weight: bold;
            //background-color: #8f8049;
            padding: 3px 15px;
            border-radius: 5px;
          }
          .key {
            //color: white;
          }
          .val {
            flex-grow: 1;
            background-color: #b9ad76;
          }
        }
      }
    }
  }
}


h1 {
  font-weight: 500;
  font-size: 2.6rem;
  position: relative;
  top: -10px;
}

h3 {
  font-size: 1.2rem;
}

.greetings h1,
.greetings h3 {
  text-align: center;
}

@media (min-width: 1024px) {
  .greetings h1,
  .greetings h3 {
    text-align: left;
  }
}

</style>
