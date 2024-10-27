<!-- ParticipantaTable.vue -->
<template>
  <div class="card">
    <div class="card-body">
      <div class="mb-3">
        <CustomInput
          v-model="searchQuery"
          type="text"
          placeholder="Search by name"
          @input="filterParticipants"
        />
      </div>
      <table class="table table-hover">
        <thead>
          <tr>
            <th>#</th>
            <th @click="sort('name')" class="sortable">
              Name
              <i :class="getSortIcon('name')"></i>
            </th>
            <th @click="sort('dob')" class="sortable">
              Date of Birth
              <i :class="getSortIcon('dob')"></i>
            </th>
            <th>Email</th>
            <th>Phone number</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr
            v-for="(participant, index) in filteredAndSortedParticipants"
            :key="participant.id"
          >
            <td>{{ index + 1 }}</td>
            <td>{{ participant.name }}</td>
            <td>{{ formatDate(participant.dob) }}</td>
            <td>{{ participant.email }}</td>
            <td>{{ participant.phone }}</td>
            <td>
              <CustomButton
                @click="editParticipant(participant)"
                class="me-2"
                variant="primary"
              >
                <i class="bi bi-pencil"></i>
              </CustomButton>
              <CustomButton
                @click="deleteParticipant(participant)"
                variant="danger"
              >
                <i class="bi bi-trash"></i>
              </CustomButton>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <Modal v-if="showEditModal" @close="closeModal">
      <EditParticipantForm
        :participant="editingParticipant"
        @update-participant="updateParticipant"
      />
    </Modal>
    <Modal v-if="showDeleteModal" @close="closeModal">
      <DeleteConfirmation
        :participant="deletingParticipant"
        @confirm-delete="confirmDelete"
        @cancel-delete="closeModal"
      />
    </Modal>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue";
import CustomButton from "./CustomButton.vue";
import CustomInput from "./CustomInput.vue";
import Modal from "./Modal.vue";
import EditParticipantForm from "./EditParticipantForm.vue";
import DeleteConfirmation from "./DeleteConfirmation.vue";

const props = defineProps<{
  participants: Array<{
    id: string;
    name: string;
    dob: string;
    email: string;
    phone: string;
  }>;
}>();

const emit = defineEmits<{
  (e: "update-participant", participant: (typeof props.participants)[0]): void;
  (e: "delete-participant", participantId: string): void;
}>();

const sortKey = ref("name");
const sortOrder = ref("asc");
const searchQuery = ref("");
const showEditModal = ref(false);
const showDeleteModal = ref(false);
const editingParticipant = ref<(typeof props.participants)[0] | null>(null);
const deletingParticipant = ref<(typeof props.participants)[0] | null>(null);

const sort = (key: string) => {
  if (sortKey.value === key) {
    sortOrder.value = sortOrder.value === "asc" ? "desc" : "asc";
  } else {
    sortKey.value = key;
    sortOrder.value = "asc";
  }
};

const getSortIcon = (key: string) => {
  if (sortKey.value !== key) return "bi bi-arrow-down-up";
  return sortOrder.value === "asc"
    ? "bi bi-sort-alpha-down"
    : "bi bi-sort-alpha-up";
};

const filteredAndSortedParticipants = computed(() => {
  let filtered = props.participants.filter((p) =>
    p.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  );

  return filtered.sort((a, b) => {
    if (sortKey.value === "name") {
      return sortOrder.value === "asc"
        ? a.name.localeCompare(b.name)
        : b.name.localeCompare(a.name);
    } else if (sortKey.value === "dob") {
      return sortOrder.value === "asc"
        ? new Date(a.dob).getTime() - new Date(b.dob).getTime()
        : new Date(b.dob).getTime() - new Date(a.dob).getTime();
    }
    return 0;
  });
});

const formatDate = (date: string): string => {
  if (!date) return "";
  const d = new Date(date);
  return `${d.getDate().toString().padStart(2, "0")}/${(d.getMonth() + 1)
    .toString()
    .padStart(2, "0")}/${d.getFullYear()}`;
};

const filterParticipants = () => {};

const editParticipant = (participant: (typeof props.participants)[0]) => {
  editingParticipant.value = { ...participant };
  showEditModal.value = true;
};

const updateParticipant = (
  updatedParticipant: (typeof props.participants)[0]
) => {
  emit("update-participant", updatedParticipant);
  closeModal();
};

const deleteParticipant = (participant: (typeof props.participants)[0]) => {
  deletingParticipant.value = participant;
  showDeleteModal.value = true;
};

const confirmDelete = () => {
  if (deletingParticipant.value) {
    emit("delete-participant", deletingParticipant.value.id);
  }
  closeModal();
};

const closeModal = () => {
  showEditModal.value = false;
  showDeleteModal.value = false;
  editingParticipant.value = null;
  deletingParticipant.value = null;
};
</script>

<style scoped>
.sortable {
  cursor: pointer;
}
.sortable:hover {
  background-color: #f8f9fa;
}
/* .bi {
  margin-left: 5px;
} */
</style>
