<script setup lang="ts">
interface Props {
  src: string;
  alt: string;
  sizes?: string;
  format?: string;
  densities?: string;
  class?: string;
  aspectRatio?: string;
}

const props = withDefaults(defineProps<Props>(), {
  sizes: 'xs:100vw sm:50vw lg:400px',
  format: 'webp',
  densities: 'x1',
  aspectRatio: 'aspect-video'
});

const emit = defineEmits<{
  (e: 'load'): void;
  (e: 'error'): void;
}>();

const imageLoaded = ref(false);
const hasError = ref(false);

const handleLoad = () => {
  imageLoaded.value = true;
  emit('load');
};

const handleError = () => {
  hasError.value = true;
  emit('error');
};
</script>

<template>
  <div 
    :class="[
      'relative overflow-hidden bg-gray-200',
      props.aspectRatio,
      props.class
    ]"
  >
    <!-- Loading Placeholder -->
    <div 
      v-show="!imageLoaded && !hasError" 
      class="absolute inset-0 animate-pulse bg-gray-300"
    />
    
    <!-- Error State -->
    <div 
      v-if="hasError" 
      class="absolute inset-0 flex items-center justify-center bg-gray-100"
    >
      <Icon 
        name="mdi:image-off" 
        size="48" 
        class="text-gray-400"
      />
    </div>

    <!-- Image -->
    <NuxtImg
      v-show="!hasError"
      :src="src"
      :alt="alt"
      :sizes="sizes"
      :format="format"
      :densities="densities"
      class="w-full h-full object-cover"
      @load="handleLoad"
      @error="handleError"
    />
  </div>
</template>
