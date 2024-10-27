<template>
  <div>
    <h2>Confirm Deletion</h2>
    <p>
      Are you sure you want to delete participant "{{ participant.name }}", "{{
        participant.email
      }}"?
    </p>
    <div>
      <CustomButton
        @click="$emit('confirm-delete')"
        class="me-2"
        variant="primary"
        >Yes</CustomButton
      >
      <CustomButton @click="$emit('cancel-delete')" variant="danger"
        >No</CustomButton
      >
    </div>
  </div>
</template>

<script setup lang="ts">
import CustomButton from "./CustomButton.vue";

defineProps<{
  participant: {
    name: string;
    email: string;
  };
}>();

defineEmits<{
  (e: "confirm-delete"): void;
  (e: "cancel-delete"): void;
}>();
</script>
