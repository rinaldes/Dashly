<script setup lang="ts">
interface Props {
  instructions: string[];
}

defineProps<Props>();
</script>

<template>
  <div>
    <h2 class="text-3xl font-semibold mb-4">Instructions</h2>
    <ul class="flex flex-col text-lg gap-4 pl-8">
      <li v-for="(instruction, index) in instructions" class="flex gap-2">
        <span
          class="flex items-center justify-center bg-dodgeroll-gold w-7 h-7 rounded-full text-white text-sm"
        >
          {{ index + 1 }}
        </span>
        <span class="flex-1">{{ instruction }}</span>
      </li>
    </ul>
  </div>
</template>
