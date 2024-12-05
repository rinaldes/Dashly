<template>
  <nav class="container mx-auto px-8 py-3 flex justify-between items-center">
    <NuxtLink to="/" class="text-2xl font-extrabold flex items-center">
      <BaseIcon name="ep:food" color="#f79f1a" class="mr-2" /> Dishly
    </NuxtLink>

    <div class="flex items-center space-x-6">
      <div class="relative w-72 flex items-center">
        <div class="relative w-full">
          <BaseInput
            v-model="searchQuery"
            type="search"
            placeholder="Search recipes..."
            class="w-full pr-10"
          />
          <BaseIcon
            name="mdi:search"
            class="absolute right-3 top-1/2 transform -translate-y-1/2"
          />
        </div>

        <!-- Search Results Dropdown -->
        <div
          v-if="isDropdownVisible && searchResults.length"
          class="absolute top-full left-0 w-full mt-2 bg-white shadow-lg rounded-lg border max-h-96 overflow-y-auto z-50"
        >
          <div
            v-for="recipe in searchResults"
            :key="recipe.id"
            @click="handleResultClick(recipe.id)"
            class="flex items-center p-3 hover:bg-gray-100 cursor-pointer"
          >
            <img
              :src="recipe.image"
              :alt="recipe.name"
              class="w-12 h-12 object-cover rounded-md mr-4"
            />
            <div>
              <p class="font-semibold text-sm">{{ recipe.name }}</p>
              <p class="text-xs text-gray-500">
                {{ recipe.difficulty }} · {{ recipe.cookTimeMinutes }} mins
              </p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </nav>
</template>

<script setup lang="ts">
import { type Recipe } from "~/types/types";

const searchQuery = ref("");
const searchResults = ref<Recipe[]>([]);
const isDropdownVisible = ref(false);
let searchTimeout: NodeJS.Timeout | null = null;

const fetchSearchResults = async () => {
  if (!searchQuery.value.trim()) {
    searchResults.value = [];
    isDropdownVisible.value = false;
    return;
  }

  try {
    const { data } = await useFetch<{ recipes: Recipe[] }>(
      `https://dummyjson.com/recipes/search?q=${searchQuery.value}&limit=8`
    );

    searchResults.value = data.value?.recipes || [];
    isDropdownVisible.value = true;
  } catch (error) {
    console.error("Search error:", error);
    searchResults.value = [];
    isDropdownVisible.value = false;
  }
};

const debouncedSearch = () => {
  if (searchTimeout) {
    clearTimeout(searchTimeout);
  }

  searchTimeout = setTimeout(() => {
    fetchSearchResults();
  }, 300);
};

watch(searchQuery, debouncedSearch);

const handleResultClick = (recipeId: number) => {
  navigateTo(`/recipes/${recipeId}`);
  isDropdownVisible.value = false;
  searchQuery.value = "";
};

// Close dropdown when clicking outside
onMounted(() => {
  const closeDropdown = (event: MouseEvent) => {
    const target = event.target as HTMLElement;
    const dropdown = document.getElementById("search-dropdown");
    const searchInput = document.getElementById("search-input");

    if (
      dropdown &&
      searchInput &&
      !dropdown.contains(target) &&
      !searchInput.contains(target)
    ) {
      isDropdownVisible.value = false;
    }
  };

  document.addEventListener("click", closeDropdown);

  onUnmounted(() => {
    document.removeEventListener("click", closeDropdown);
  });
});

onUnmounted(() => {
  if (searchTimeout) {
    clearTimeout(searchTimeout);
  }
});
</script>
