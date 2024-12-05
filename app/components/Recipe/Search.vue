<script setup lang="ts">
import { ref, watch } from "vue";
import { useFetch } from "#app";
import type { RecipeResponse } from "~/types/types";

const searchQuery = ref("");
const debouncedSearchQuery = ref("");

// Create a debounce function
const debounce = (func: Function, delay: number) => {
  let timeoutId: NodeJS.Timeout;
  return (...args: any[]) => {
    clearTimeout(timeoutId);
    timeoutId = setTimeout(() => func(...args), delay);
  };
};

// Watch for changes in searchQuery and debounce the update
watch(
  searchQuery,
  debounce((value: string) => {
    debouncedSearchQuery.value = value;
  }, 500)
);

// Fetch search results based on the debounced query
const { data: searchResults } = useFetch<RecipeResponse>(
  `https://dummyjson.com/recipes/search?q=${debouncedSearchQuery.value}&limit=8`
);

const handleQueryUpdate = (query: string) => {
  searchQuery.value = query;
};
</script>

<template>
  <div class="relative">
    <RecipeSearchInput
      :model-value="searchQuery"
      @update:query="handleQueryUpdate"
    />
    <RecipeSearchResults
      :recipes="searchResults?.recipes || []"
      :show="!!debouncedSearchQuery"
    />
  </div>
</template>
