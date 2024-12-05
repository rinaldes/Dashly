<script setup lang="ts">
  import type { RecipeResponse } from '~/types/types';
  import { ref, computed, watch } from 'vue';
  
  const currentPage = ref(1);
  const limit = 12;
  const skip = computed(() => (currentPage.value - 1) * limit);
  const heroImageLoaded = ref(false);
  const searchQuery = ref('');
  const debouncedSearchQuery = ref('');

  const debounce = (func: Function, delay: number) => {
    let timeoutId: NodeJS.Timeout;
    return (...args: any[]) => {
      clearTimeout(timeoutId);
      timeoutId = setTimeout(() => func(...args), delay);
    };
  };

  watch(searchQuery, debounce((value: string) => {
    debouncedSearchQuery.value = value;
    currentPage.value = 1; // Reset to first page when searching
  }, 500));

  const { data, error } = await useFetch<RecipeResponse>(() => 
    `https://dummyjson.com/recipes/search?q=${debouncedSearchQuery.value}&limit=${limit}&skip=${skip.value}`
  );

  const totalPages = computed(() => Math.ceil((data.value?.total || 0) / limit));

  function nextPage() {
    if (currentPage.value < totalPages.value) {
      currentPage.value++;
    }
  }

  function prevPage() {
    if (currentPage.value > 1) {
      currentPage.value--;
    }
  }

  useSeoMeta({
    title: "Nuxtcipes",
    description: "Recipes for you to cook!",
    ogTitle: "Nuxtcipes",
    ogDescription: "Recipes for you to cook!",
    ogImage: "/nuxt-course-hero.png",
    ogUrl: `http:localhost:3000`,
    twitterTitle: "Nuxtcipes",
    twitterDescription: "Recipes for you to cook!",
    twitterImage: "nuxt-course-hero.png",
    twitterCard: "summary",
  });
</script>

<template>
  <main>
    <section class="bg-[#f1f1f1]">
      <div class="container flex flex-col lg:flex-row items-center py-20 gap-10">
        <div class="flex-1 order-2 lg:order-1 text-center lg:text-left">
          <h1 class="text-4xl lg:text-6xl font-extrabold mb-6 text-balance">
            Dishly: Unleash Your Inner Chef Today!
          </h1>
          <p class="text-xl lg:text-2xl mb-8 text-balance">
            Discover recipes helping you to find the easiest way to cook.
          </p>
          <button
            class="px-4 py-2 text-white self-start bg-dodgeroll-gold rounded-md text-lg cursor-pointer"
            @click="$refs.searchSection.scrollIntoView({ behavior: 'smooth' })"
          >
            Browse Recipes
          </button>
        </div>
        <div class="flex-1 order-1 lg:order-2">
          <div class="relative aspect-[4/3] w-full">
            <div 
              v-show="!heroImageLoaded" 
              class="absolute inset-0 animate-pulse bg-gray-300 rounded-md"
            />
            <NuxtImg
              sizes="xs:100vw sm:667px"
              src="/nuxt-course-hero.png"
              format="webp"
              densities="x1"
              alt=""
              class="w-full h-full object-fill rounded-md"
              @load="heroImageLoaded = true"
            />
          </div>
        </div>
      </div>
    </section>

    <section ref="searchSection" class="py-20 container px-32">
      <h2 class="text-3xl lg:text-5xl mb-2">Discover, Create, Share</h2>
      <p class="text-lg lg:text-xl mb-8">Check out our most popular recipes!</p>

      <!-- Search Bar with Animations -->
      <div class="relative mb-8 max-w-xl group">
        <input
          v-model="searchQuery"
          type="text"
          placeholder="Search recipes..."
          class="w-full px-4 py-3 pr-12 rounded-md border border-gray-300 
            focus:outline-none focus:border-dodgeroll-gold focus:ring-1 focus:ring-dodgeroll-gold
            transition-all duration-300 ease-in-out
            group-focus-within:shadow-lg group-focus-within:border-dodgeroll-gold"
        />
        <Icon 
          name="mdi:magnify" 
          class="absolute right-4 top-1/2 -translate-y-1/2 
            text-gray-400 group-focus-within:text-dodgeroll-gold 
            transition-colors duration-300"
          size="24"
        />
      </div>

      <div v-if="error" class="text-xl">
        Opps, something went wrong. Please try again later
      </div>
      <template v-else>
        <div v-if="data?.recipes?.length" class="grid grid-cols-1 md:grid-cols-3 lg:grid-cols-4 gap-8">
          <RecipeCard
            v-for="recipe in data.recipes"
            :key="recipe.id"
            :recipe="recipe"
          />
        </div>
        <div v-else class="text-center py-12">
          <Icon 
            name="mdi:food-off" 
            class="text-gray-400 mx-auto mb-4"
            size="48"
          />
          <p class="text-xl text-gray-600">No recipes found matching your search.</p>
          <p class="text-gray-500 mt-2">Try different keywords or clear the search.</p>
        </div>
      </template>

      <!-- Pagination Controls -->
      <div class="flex items-center gap-4 mt-8 justify-between">
        <div class="flex items-center gap-2">
          <button 
            @click="currentPage = 1" 
            :disabled="currentPage === 1"
            class="p-2 text-dodgeroll-gold disabled:text-gray-300 hover:bg-gray-100 rounded-md transition-colors"
            title="First Page"
          >
            <Icon name="mdi:chevron-double-left" size="24" />
          </button>
          <button 
            @click="prevPage" 
            :disabled="currentPage === 1"
            class="p-2 text-dodgeroll-gold disabled:text-gray-300 hover:bg-gray-100 rounded-md transition-colors"
            title="Previous Page"
          >
            <Icon name="mdi:chevron-left" size="24" />
          </button>
          <span class="text-lg font-medium px-4">{{ currentPage }}</span>
          <button 
            @click="nextPage" 
            :disabled="currentPage === totalPages"
            class="p-2 text-dodgeroll-gold disabled:text-gray-300 hover:bg-gray-100 rounded-md transition-colors"
            title="Next Page"
          >
            <Icon name="mdi:chevron-right" size="24" />
          </button>
          <button 
            @click="currentPage = totalPages" 
            :disabled="currentPage === totalPages"
            class="p-2 text-dodgeroll-gold disabled:text-gray-300 hover:bg-gray-100 rounded-md transition-colors"
            title="Last Page"
          >
            <Icon name="mdi:chevron-double-right" size="24" />
          </button>
        </div>
        <span class="text-lg text-gray-500">Page {{ currentPage }} of {{ totalPages }} pages</span>
      </div>
    </section>
  </main>
</template>