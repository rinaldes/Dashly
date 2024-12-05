<script setup lang="ts">
import type { Recipe } from "~/types/types";

interface Props {
  recipe: Recipe;
}

defineProps<Props>();
</script>

<template>
  <div class="flex flex-col mb-6">
    <div class="flex justify-between items-start mb-4">
      <h2 class="text-5xl font-semibold">{{ recipe.name }}</h2>
    </div>

    <!-- Recipe Quick Info -->

    <div class="w-full flex flex-wrap gap-8 gap-y-4 text-xl mb-6">
      <div class="flex items-center gap-2" title="Cooking Time">
        <Icon name="mdi:clock-time-eight-outline" class="text-dodgeroll-gold" />
        <span>{{ recipe?.cookTimeMinutes }} mins</span>
      </div>
      <div class="flex items-center gap-2" title="Calories">
        <Icon name="mdi:fire" class="text-dodgeroll-gold" />
        <span>{{ recipe?.caloriesPerServing }} cal</span>
      </div>
      <div class="flex items-center gap-2" title="Rating">
        <Icon name="mdi:star" class="text-dodgeroll-gold" />
        <span>{{ recipe?.rating }} ({{ recipe?.reviewCount }} reviews)</span>
      </div>
      <div class="flex items-center gap-2" title="Servings">
        <Icon name="mdi:bowl-mix" class="text-dodgeroll-gold" />
        <span>{{ recipe?.servings }} servings</span>
      </div>
      <div class="flex items-center gap-2" title="Cuisine">
        <Icon name="mdi:silverware-fork-knife" class="text-dodgeroll-gold" />
        <span>{{ recipe?.cuisine }}</span>
      </div>
      <div class="flex items-center gap-2" title="Meal Type">
        <Icon name="mdi:food-outline" class="text-dodgeroll-gold" />
        <span>{{ recipe?.mealType?.join(", ") }}</span>
      </div>
      <div
        v-if="recipe?.difficulty"
        class="flex items-center gap-2"
        title="Difficulty"
      >
        <Icon name="mdi:chef-hat" class="text-dodgeroll-gold" />
        <span>{{ recipe?.difficulty }}</span>
      </div>
    </div>
    <hr />
  </div>
</template>
