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
        <Icon
          name="mdi:arrow-left"
          size="24"
          class="group-hover:-translate-x-1 transition-transform"
        />
        <span>Back to Recipes</span>
      </NuxtLink>
    </div>

    <!-- Header -->
    <div class="flex flex-col mb-6">
      <div class="flex justify-between items-start mb-4">
        <h2 class="text-5xl font-semibold">{{ data?.name }}</h2>
      </div>

      <!-- Recipe Quick Info -->
      <div class="w-full flex flex-wrap gap-8 gap-y-4 text-xl mb-6">
        <div class="flex items-center gap-1" title="Cooking Time">
          <Icon
            name="mdi:clock-time-eight-outline"
            class="text-dodgeroll-gold"
          />
          <span>{{ data?.cookTimeMinutes }} mins</span>
        </div>
        <div class="flex items-center gap-1" title="Calories">
          <Icon name="mdi:fire" class="text-dodgeroll-gold" />
          <span>{{ data?.caloriesPerServing }} cal</span>
        </div>
        <div class="flex items-center gap-1" title="Rating">
          <Icon name="mdi:star" class="text-dodgeroll-gold" />
          <span>{{ data?.rating }} ({{ data?.reviewCount }} reviews)</span>
        </div>
        <div class="flex items-center gap-1" title="Servings">
          <Icon name="mdi:bowl-mix" class="text-dodgeroll-gold" />
          <span>{{ data?.servings }} servings</span>
        </div>
        <div class="flex items-center gap-1" title="Cuisine">
          <Icon name="mdi:silverware-fork-knife" class="text-dodgeroll-gold" />
          <span>{{ data?.cuisine }}</span>
        </div>
        <div class="flex items-center gap-1" title="Meal Type">
          <Icon name="mdi:food-outline" class="text-dodgeroll-gold" />
          <span>{{ data?.mealType?.join(", ") }}</span>
        </div>
        <div
          v-if="data?.difficulty"
          class="flex items-center gap-1"
          title="Difficulty"
        >
          <Icon name="mdi:chef-hat" class="text-dodgeroll-gold" />
          <span>{{ data?.difficulty }}</span>
        </div>
      </div>
      <hr />
    </div>

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

    <!-- Ingredients -->
    <div class="mb-12">
      <h2 class="text-3xl font-semibold mb-4">Ingredients</h2>
      <ul class="grid grid-cols-1 md:grid-cols-2 gap-2 text-lg pl-8">
        <li v-for="ingredient in data?.ingredients">
          <label
            class="flex gap-2 items-center cursor-pointer hover:text-dodgeroll-gold"
          >
            <input class="hidden peer" type="checkbox" />
            <div
              class="relative w-6 h-6 rounded-full border-2 border-dodgeroll-gold flex items-center justify-center peer-checked:after:absolute peer-checked:after:w-4 peer-checked:after:h-4 peer-checked:after:bg-dodgeroll-gold peer-checked:after:rounded-full"
            ></div>
            <span class="peer-checked:line-through">
              {{ ingredient }}
            </span>
          </label>
        </li>
      </ul>
    </div>

    <!-- Instructions -->
    <div>
      <h2 class="text-3xl font-semibold mb-4">Instructions</h2>
      <ul class="flex flex-col text-lg gap-4 pl-8">
        <li
          v-for="(instruction, index) in data?.instructions"
          class="flex gap-2"
        >
          <span
            class="flex items-center justify-center bg-dodgeroll-gold w-7 h-7 rounded-full text-white text-sm"
          >
            {{ index + 1 }}
          </span>
          <span class="flex-1">{{ instruction }}</span>
        </li>
      </ul>
    </div>

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
