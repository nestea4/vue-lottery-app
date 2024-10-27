<template>
  <div class="container mt-4">
    <WinnersBlock
      :winners="winners"
      :availableParticipants="availableParticipants"
      @select-winner="selectWinner"
      @remove-winner="removeWinner"
    />
    <RegistrationForm
      ref="registrationForm"
      @register-participant="registerParticipant"
    />
    <ParticipantsTable
      :participants="participants"
      @update-participant="updateParticipant"
      @delete-participant="deleteParticipant"
    />
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, watch } from "vue";
import WinnersBlock from "./WinnersBlock.vue";
import RegistrationForm from "./RegistrationForm.vue";
import ParticipantsTable from "./ParticipantsTable.vue";

interface Participant {
  id: string;
  name: string;
  dob: string;
  email: string;
  phone: string;
}

const participants = ref<Participant[]>([]);
const winners = ref<Participant[]>([]);
const registrationForm = ref<InstanceType<typeof RegistrationForm> | null>(
  null
);

const availableParticipants = computed(() => {
  return participants.value.filter(
    (p) => !winners.value.some((w) => w.id === p.id)
  );
});

onMounted(() => {
  loadFromLocalStorage();
});

watch(
  [participants, winners],
  () => {
    saveToLocalStorage();
  },
  { deep: true }
);

const loadFromLocalStorage = () => {
  const storedParticipants = localStorage.getItem("participants");
  const storedWinners = localStorage.getItem("winners");

  if (storedParticipants) {
    participants.value = JSON.parse(storedParticipants);
  }
  if (storedWinners) {
    winners.value = JSON.parse(storedWinners);
  }
};

const saveToLocalStorage = () => {
  localStorage.setItem("participants", JSON.stringify(participants.value));
  localStorage.setItem("winners", JSON.stringify(winners.value));
};

const registerParticipant = (newParticipant: Omit<Participant, "id">) => {
  if (participants.value.some((p) => p.email === newParticipant.email)) {
    registrationForm.value?.showEmailExistsError();
    return;
  }
  const participant = {
    ...newParticipant,
    id: Date.now().toString(),
  };
  participants.value.push(participant);
};

const selectWinner = () => {
  if (availableParticipants.value.length > 0 && winners.value.length < 3) {
    const winnerIndex = Math.floor(
      Math.random() * availableParticipants.value.length
    );
    winners.value.push(availableParticipants.value[winnerIndex]);
  }
};

const removeWinner = (winner: Participant) => {
  winners.value = winners.value.filter((w) => w.id !== winner.id);
};

const updateParticipant = (updatedParticipant: Participant) => {
  const index = participants.value.findIndex(
    (p) => p.id === updatedParticipant.id
  );
  if (index !== -1) {
    participants.value[index] = updatedParticipant;
  }
};

const deleteParticipant = (participantId: string) => {
  participants.value = participants.value.filter((p) => p.id !== participantId);
  winners.value = winners.value.filter((w) => w.id !== participantId);
};
</script>
