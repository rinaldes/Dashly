<script setup lang="ts">
interface Props {
  image: string;
  alt?: string;
}

const props = defineProps<Props>();

const emit = defineEmits<{
  (e: 'load'): void;
}>();

const imageLoaded = ref(false);

const onImageLoad = () => {
  imageLoaded.value = true;
  emit('load');
};
</script>

<template>
  <div class="relative w-full h-48 overflow-hidden">
    <img 
      :src="image" 
      :alt="alt || 'Recipe Image'" 
      class="w-full h-full object-cover transition-transform duration-300 group-hover:scale-110"
      @load="onImageLoad"
    />
    
    <!-- Optional loading state -->
    <div 
      v-if="!imageLoaded" 
      class="absolute inset-0 bg-gray-200 animate-pulse"
    ></div>
  </div>
</template>

<style scoped>
/* Add any specific styling for the recipe card image */
</style>
