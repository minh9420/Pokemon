<script setup lang="ts">
import {useRoute, useRouter} from "vue-router";

const router = useRouter()
const route = useRoute()
// router.replace({ query: { search: 'search' } })
console.log('alo', route.params.id)
const goToAbout = () => {
  console.log('goToAbout')
  router.push({ path: '/about'})
  // router.replace({ query: { search: 'abc' } })
}
</script>
<template>
  hello
  <div @click="goToAbout">button</div>
</template>