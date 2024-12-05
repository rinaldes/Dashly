<script setup lang="ts">
interface Props {
  ingredients: string[];
}

defineProps<Props>();
</script>

<template>
  <div class="mb-12">
    <h2 class="text-3xl font-semibold mb-4">Ingredients</h2>
    <ul class="grid grid-cols-1 md:grid-cols-2 gap-2 text-lg pl-8">
      <li v-for="ingredient in ingredients">
        <label class="flex gap-2 items-center cursor-pointer hover:text-dodgeroll-gold">
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
</template>
