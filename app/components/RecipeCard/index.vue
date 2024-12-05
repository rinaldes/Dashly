<script setup lang="ts">
import { type Recipe } from "~/types/types";

interface Props {
  recipe: Recipe;
}

const props = defineProps<Props>();

// Extract meta information for easier composition
const metaInfo = computed(() => ({
  cookTimeMinutes: props.recipe.cookTimeMinutes,
  caloriesPerServing: props.recipe.caloriesPerServing,
  rating: props.recipe.rating,
  reviewCount: props.recipe.reviewCount,
}));

// Extract image information
const imageInfo = computed(() => ({
  src: props.recipe.image,
  alt: props.recipe.name,
}));
</script>

<template>
  <NuxtLink
    :to="`/recipes/${props.recipe.id}`"
    class="flex flex-col shadow rounded-md group bg-white hover:bg-gray-100 transition-all duration-300 hover:scale-[1.05] hover:shadow-lg"
  >
    <RecipeCardImage :image="imageInfo.src" :alt="imageInfo.alt" />

    <div class="flex flex-col py-6 px-4 flex-1">
      <RecipeCardTitle :title="props.recipe.name" class="mb-2" />
      <RecipeCardMeta
        :cook-time-minutes="metaInfo.cookTimeMinutes"
        :calories-per-serving="metaInfo.caloriesPerServing"
        :rating="metaInfo.rating"
        :review-count="metaInfo.reviewCount"
        :title="props.recipe.name"
        class="mb-2 mt-auto"
      />
    </div>
  </NuxtLink>
</template>

<style scoped>
/* Add any specific styling for the recipe card */
</style>
