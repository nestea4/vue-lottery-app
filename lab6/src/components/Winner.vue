<template>
  <span class="badge bg-info text-light">
    {{ winner.name }}
    <button
      @click="$emit('remove')"
      class="btn-close btn-close-white ms-2"
      aria-label="Remove"
    ></button>
  </span>
</template>

<script setup lang="ts">
defineProps<{
  winner: {
    id: string;
    name: string;
  };
}>();

defineEmits<{
  (e: "remove"): void;
}>();
</script>
