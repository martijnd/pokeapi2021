<template>
  <div class="min-h-screen bg-blue-900 text-white">
    <div v-if="fetchState.pending">Loading...</div>
    <div v-if="!fetchState.pending">
      <table>
        <thead>
          <th>
            <td>Name</td>
          </th>
        </thead>
        <tbody>
          <tr v-for="pokeman of pokemon.results" :key="pokeman.name">
            <td><nuxt-link :to="`${pokeman.url}`">{{pokeman.name}}</nuxt-link></td>
          </tr>
        </tbody>
      </table>
      <button @click="offset += 20">Next</button>
    </div>
  </div>
</template>

<script lang="ts">
import {
  defineComponent,
  ref,
  useContext,
  useFetch,
  useRouter,
  watch,
} from '@nuxtjs/composition-api'

export default defineComponent({
  setup() {
    const pokemon = ref([])
    const { $axios, query } = useContext()
    const router = useRouter();
    const offset = ref(+query.value.offset || 0);
    const { fetchState, fetch } = useFetch(async () => {
      pokemon.value = await $axios.get(`pokemon?offset=${offset.value}`).then((res) => res.data)
    })

    watch(() => offset.value, () => {
      router.push(`?offset=${offset.value}`)
      fetch();
    })

    return {
      pokemon,
      fetchState,
      offset
    }
  },
})
</script>
