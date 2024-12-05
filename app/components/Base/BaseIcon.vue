<script setup lang="ts">
interface Props {
  name: string;
  size?: string | number;
  color?: string;
  class?: string;
}

const props = withDefaults(defineProps<Props>(), {
  size: "24",
  color: "currentColor",
});

// Convert size to string and add 'px' if it's a number
const iconSize = computed(() => {
  if (typeof props.size === "number" || !isNaN(Number(props.size))) {
    return `${props.size}px`;
  }
  return props.size;
});
</script>

<template>
  <Icon
    :name="name"
    :size="iconSize"
    :style="{ color: props.color }"
    :class="['inline-block', props.class]"
  />
</template>
