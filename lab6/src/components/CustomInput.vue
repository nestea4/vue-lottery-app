<!-- CustomInput.vue -->
<template>
  <div class="mb-3">
    <input
      :value="modelValue"
      @input="
        $emit('update:modelValue', ($event.target as HTMLInputElement).value)
      "
      :type="type"
      :placeholder="placeholder"
      :class="[
        'form-control',
        { 'is-invalid': error, 'is-valid': modelValue && !error },
      ]"
      @blur="$emit('blur')"
    />
    <div v-if="error" class="invalid-feedback">
      {{ error }}
    </div>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  modelValue: string;
  type: string;
  placeholder?: string;
  error?: string;
}>();

defineEmits<{
  (e: "update:modelValue", value: string): void;
  (e: "blur"): void;
}>();
</script>
