<script setup lang="ts">
interface Props {
  modelValue: string;
  placeholder?: string;
  type?: string;
  name?: string;
  id?: string;
  class?: string;
  disabled?: boolean;
  required?: boolean;
  autocomplete?: string;
}

const props = withDefaults(defineProps<Props>(), {
  type: "text",
  placeholder: "",
  disabled: false,
  required: false,
  autocomplete: "off",
});

const emit = defineEmits<{
  (e: "update:modelValue", value: string): void;
  (e: "focus", event: FocusEvent): void;
  (e: "blur", event: FocusEvent): void;
}>();

const updateValue = (event: Event) => {
  const target = event.target as HTMLInputElement;
  emit("update:modelValue", target.value);
};
</script>

<template>
  <div class="relative group">
    <input
      :value="modelValue"
      :type="type"
      :name="name"
      :id="id"
      :placeholder="placeholder"
      :disabled="disabled"
      :required="required"
      :autocomplete="autocomplete"
      :class="[
        'w-full px-4 py-2 rounded-md border border-gray-300',
        'focus:outline-none focus:border-dodgeroll-gold focus:ring-1 focus:ring-dodgeroll-gold',
        'transition-all duration-300 ease-in-out',
        'disabled:bg-gray-100 disabled:cursor-not-allowed',
        'group-focus-within:shadow-lg group-focus-within:border-dodgeroll-gold',
        props.class,
      ]"
      @input="updateValue"
      @focus="$emit('focus', $event)"
      @blur="$emit('blur', $event)"
    />
    <slot name="icon-right">
      <!-- Right icon slot -->
    </slot>
  </div>
</template>
