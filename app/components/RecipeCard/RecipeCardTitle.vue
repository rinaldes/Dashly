<script setup lang="ts">
interface Props {
  title: string;
}

defineProps<Props>();
</script>

<template>
  <h3 class="text-xl font-semibold mb-2">
    {{ title }}
  </h3>
</template>

<style scoped>
/* Add any specific styling for the recipe card title */
</style>
