<script setup lang="ts">
import { ref } from 'vue'
import debounce from "debounce";

const URL = 'https://pokeapi.co/api/v2/pokemon'

const query = ref('')
const isLoading = ref(false)

const results = ref<string[]>([])

const onInputChange = () => {
  results.value.splice(0)

  if (!query.value || !query.value.trim()) {
    return
  }

  if (query.value.trim().length < 2) {
    return;
  }

  debounceAndSearch()
}

const debounceAndSearch = debounce(async () => {
  isLoading.value = true
  // adding a timeout just to simulate a delay response from the endpoint
  setTimeout(() => searchResults(query.value), 500)
}, 500)

const searchResults = async (query: string) => {
  const response = await (await fetch(URL)).json()
  const filteredResults: any[] = response.results.filter(({ name }: any) => name.includes(query))
  results.value = filteredResults.map(({ name }: any) => name)
  isLoading.value = false
}
</script>

<template>
  <main>
    <div class="container">
      <label for="text">Start typing</label>
      <input id="text" type="text" v-model="query" @keyup="onInputChange">
      <span v-if="isLoading" class="loading">Loading results...</span>
      <div v-if="results.length" class="item-list">
        <div v-for="result in results" class="item">{{ result }}</div>
      </div>
    </div>
  </main>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
} 
.list-item {
  width: 100%;
}

.item {
  padding: 0 0.5rem;
  border-radius: 5px;
  border: 1px solid gray;
  margin-bottom: 1px
}
</style>
