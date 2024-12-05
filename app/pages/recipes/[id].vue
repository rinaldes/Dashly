<script setup lang="ts">
import type { Recipe, RecipeResponse } from "~/types/types";
import { ref } from "vue";

const { id } = useRoute().params;
const imageLoaded = ref(false);

const { data, error } = await useFetch<Recipe>(
  `https://dummyjson.com/recipes/${id}`
);

const { data: similarRecipes } = await useFetch<RecipeResponse>(
  () =>
    `https://dummyjson.com/recipes/meal-type/${encodeURIComponent(
      data.value?.mealType?.[0] || ""
    )}`,
  {
    watch: [data],
    transform: (response: RecipeResponse) => ({
      ...response,
      recipes: (response?.recipes || [])
        .filter((recipe) => recipe.id !== Number(id))
        .slice(0, 3),
    }),
  }
);

if (error.value) {
  throw createError({
    statusCode: error.value?.statusCode,
    statusMessage: error.value?.statusMessage,
  });
}

useSeoMeta({
  title: data.value?.name,
  description: "Recipes for you to cook!",
  ogTitle: data.value?.name,
  ogDescription: "Recipes for you to cook!",
  ogImage: data.value?.image,
  ogUrl: `http:localhost:3001/recipes/${data.value?.id}`,
  twitterTitle: data.value?.name,
  twitterDescription: "Recipes for you to cook!",
  twitterImage: data.value?.image,
  twitterCard: "summary",
});
</script>

<template>
  <div class="flex flex-col max-w-screen-lg container py-20">
    <!-- Back Button -->
    <div class="mb-6">
      <NuxtLink
        to="/"
        class="flex items-center gap-2 text-lg hover:text-dodgeroll-gold transition-colors group"
      >
        <BaseIcon
          name="mdi:arrow-left"
          size="24"
          class="group-hover:-translate-x-1 transition-transform"
        />
        <span>Back to Recipes</span>
      </NuxtLink>
    </div>

    <RecipeHeader v-if="data" :recipe="data" />

    <!-- Image -->
    <div class="relative aspect-[16/9] w-full mb-12 overflow-hidden rounded-md">
      <div
        v-show="!imageLoaded"
        class="absolute inset-0 animate-pulse bg-gray-300 rounded-md"
      />
      <NuxtImg
        :src="data?.image"
        densities="x1"
        sizes="xs:100vw sm:100vw md:100vw lg:100vw"
        class="w-full h-full object-cover shadow-sm transition-transform duration-500 hover:scale-110"
        alt=""
        loading="lazy"
        @load="imageLoaded = true"
      />
    </div>

    <RecipeIngredients
      v-if="data?.ingredients"
      :ingredients="data.ingredients"
    />
    <RecipeInstructions
      v-if="data?.instructions"
      :instructions="data.instructions"
    />

    <!-- Recommendations Section -->
    <div class="mt-20 border-t pt-16">
      <h2 class="text-3xl font-semibold mb-8">You Might Also Like</h2>
      <div
        v-if="similarRecipes?.recipes?.length"
        class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-8"
      >
        <RecipeCard
          v-for="recipe in similarRecipes.recipes"
          :key="recipe.id"
          :recipe="recipe"
          class="transform hover:scale-105 transition-transform duration-300"
        />
      </div>
      <div v-else class="text-center text-gray-500 py-8">
        Loading recommendations...
      </div>
    </div>
  </div>
</template>
