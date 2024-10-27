<template>
  <div class="card mb-4">
    <div class="card-body d-flex justify-content-between align-items-center">
      <div class="d-flex flex-grow-1 border p-2" style="min-height: 38px">
        <Winner
          v-for="winner in winners"
          :key="winner.id"
          :winner="winner"
          @remove="$emit('remove-winner', winner)"
          class="me-2"
        />
        <div v-if="winners.length === 0" class="text-muted">Winners</div>
      </div>
      <CustomButton
        @click="$emit('select-winner')"
        :disabled="winners.length >= 3 || availableParticipants.length === 0"
        variant="primary"
        class="button"
      >
        New winner
      </CustomButton>
    </div>
  </div>
</template>

<script setup lang="ts">
import Winner from "./Winner.vue";
import CustomButton from "./CustomButton.vue";

defineProps<{
  winners: Array<{ id: string; name: string }>;
  availableParticipants: Array<any>;
}>();

defineEmits<{
  (e: "select-winner"): void;
  (e: "remove-winner", winner: { id: string; name: string }): void;
}>();
</script>

<style scoped>
.button {
  margin-left: 10px;
}
</style>
