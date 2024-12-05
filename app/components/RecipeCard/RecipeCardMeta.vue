<script setup lang="ts">
interface Props {
  cookTimeMinutes: number;
  caloriesPerServing: number;
  rating: number;
  reviewCount: number;
  title?: string;
}

const props = defineProps<Props>();
</script>

<template>
  <div class="font-normal w-full flex gap-4 text-md">
    <div class="flex items-center gap-2">
      <div class="flex items-center gap-1">
        <BaseIcon name="mdi:clock-time-eight-outline" color="#f79f1a" />
        <span>{{ cookTimeMinutes }} min</span>
      </div>

      <div class="flex items-center gap-1 ml-4">
        <BaseIcon name="mdi:fire" color="#ff4500" />
        <span>{{ caloriesPerServing }} </span>
      </div>
    </div>

    <div class="flex items-center gap-2">
      <BaseIcon name="mdi:star" color="#ffc107" />
      <span>{{ rating.toFixed(1) }} ({{ reviewCount }})</span>
    </div>
  </div>
</template>

<style scoped>
/* Add any specific styling for the recipe card meta information */
</style>
