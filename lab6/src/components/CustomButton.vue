<!-- CustomButton.vue -->
<template>
  <button
    :class="['btn', `btn-${variant}`, { 'btn-sm': small }]"
    :disabled="disabled"
    @click="$emit('click')"
  >
    <slot></slot>
  </button>
</template>

<script setup lang="ts">
defineProps<{
  variant?: "primary" | "secondary" | "danger" | "success";
  small?: boolean;
  disabled?: boolean;
}>();

defineEmits<{
  (e: "click"): void;
}>();
</script>
