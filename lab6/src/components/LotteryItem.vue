<template>
  <div class="container mt-4">
    <!-- Winners -->
    <div class="card mb-4">
      <div class="card-body d-flex justify-content-between align-items-center">
        <div class="d-flex flex-grow-1 border p-2" style="min-height: 38px">
          <div v-for="winner in winners" :key="winner.name" class="me-2">
            <span class="badge bg-info text-light">
              {{ winner.name }}
              <button
                @click="removeWinner(winner)"
                class="btn-close btn-close-white ms-2"
                aria-label="Remove"
              ></button>
            </span>
          </div>
          <div v-if="winners.length === 0" class="text-muted">Winners</div>
        </div>
        <button
          @click="selectWinner"
          class="btn btn-primary ms-2"
          :disabled="winners.length >= 3 || availableParticipants.length === 0"
        >
          New winner
        </button>
      </div>
    </div>

    <!-- Registration -->
    <div class="card mb-4">
      <div class="card-body">
        <h5 class="card-title">REGISTER FORM</h5>
        <p class="text-muted">Please fill in all the fields.</p>
        <form @submit.prevent="registerParticipant">
          <div class="mb-3">
            <input
              v-model="form.name"
              type="text"
              class="form-control"
              placeholder="Enter user name"
              :class="{
                'is-invalid': errors.name,
                'is-valid': form.name && !errors.name,
              }"
              @blur="validateField('name')"
            />
            <div v-if="errors.name" class="invalid-feedback">
              {{ errors.name }}
            </div>
          </div>
          <div class="mb-3">
            <input
              v-model="form.dob"
              type="date"
              class="form-control"
              :class="{
                'is-invalid': errors.dob,
                'is-valid': form.dob && !errors.dob,
              }"
              @blur="validateField('dob')"
            />
            <div v-if="errors.dob" class="invalid-feedback">
              {{ errors.dob }}
            </div>
          </div>
          <div class="mb-3">
            <input
              v-model="form.email"
              type="email"
              class="form-control"
              placeholder="Enter email"
              :class="{
                'is-invalid': errors.email,
                'is-valid': form.email && !errors.email,
              }"
              @blur="validateField('email')"
            />
            <div v-if="errors.email" class="invalid-feedback">
              {{ errors.email }}
            </div>
          </div>
          <div class="mb-3">
            <input
              v-model="form.phone"
              type="tel"
              class="form-control"
              placeholder="Enter Phone number"
              :class="{
                'is-invalid': errors.phone,
                'is-valid': form.phone && !errors.phone,
              }"
              @blur="validateField('phone')"
            />
            <div v-if="errors.phone" class="invalid-feedback">
              {{ errors.phone }}
            </div>
          </div>
          <button type="submit" class="btn btn-primary">Save</button>
        </form>
      </div>
    </div>

    <!-- Participants -->
    <div class="card">
      <div class="card-body">
        <table class="table">
          <thead>
            <tr>
              <th>#</th>
              <th>Name</th>
              <th>Date of Birth</th>
              <th>Email</th>
              <th>Phone number</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(participant, index) in participants" :key="index">
              <td>{{ index + 1 }}</td>
              <td>{{ participant.name }}</td>
              <td>{{ formatDate(participant.dob) }}</td>
              <td>{{ participant.email }}</td>
              <td>{{ participant.phone }}</td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, reactive, computed } from "vue";

interface Participant {
  name: string;
  dob: string;
  email: string;
  phone: string;
}

interface FormErrors {
  name?: string;
  dob?: string;
  email?: string;
  phone?: string;
}

const winners = ref<Participant[]>([]);
const participants = ref<Participant[]>([]);
const form = reactive<Participant>({
  name: "",
  dob: "",
  email: "",
  phone: "",
});
const errors = reactive<FormErrors>({});

const availableParticipants = computed(() => {
  return participants.value.filter(
    (p) => !winners.value.some((w) => w.name === p.name)
  );
});

const validateField = (field: keyof Participant) => {
  errors[field] = "";

  switch (field) {
    case "name":
      if (!form.name.trim()) {
        errors.name = "Name is required";
      }
      break;
    case "dob":
      if (!form.dob) {
        errors.dob = "Date of Birth is required";
      } else {
        const dob = new Date(form.dob);
        if (dob > new Date()) {
          errors.dob = "Date of Birth cannot be in the future";
        }
      }
      break;
    case "email":
      if (!form.email.trim()) {
        errors.email = "Email is required";
      } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) {
        errors.email = "Invalid email format";
      }
      break;
    case "phone":
      if (!form.phone.trim()) {
        errors.phone = "Phone number is required";
      } else if (!/^\d{10}$/.test(form.phone.replace(/\D/g, ""))) {
        errors.phone = "Invalid phone number format";
      }
      break;
  }
};

const validateForm = (): boolean => {
  let isValid = true;
  (Object.keys(form) as Array<keyof Participant>).forEach((field) => {
    validateField(field);
    if (errors[field]) isValid = false;
  });
  return isValid;
};

const registerParticipant = () => {
  if (validateForm()) {
    participants.value.push({ ...form });
    resetForm();
  }
};

const resetForm = () => {
  Object.keys(form).forEach((key) => (form[key as keyof Participant] = ""));
  Object.keys(errors).forEach((key) => (errors[key as keyof FormErrors] = ""));
};

const selectWinner = () => {
  if (availableParticipants.value.length > 0 && winners.value.length < 3) {
    const winnerIndex = Math.floor(
      Math.random() * availableParticipants.value.length
    );
    const winner = availableParticipants.value[winnerIndex];
    winners.value.push({ ...winner });
  }
};

const removeWinner = (winner: Participant) => {
  const index = winners.value.findIndex((w) => w.name === winner.name);
  if (index > -1) {
    winners.value.splice(index, 1);
  }
};

const formatDate = (date: string): string => {
  if (!date) return "";
  const d = new Date(date);
  return `${d.getDate().toString().padStart(2, "0")}/${(d.getMonth() + 1)
    .toString()
    .padStart(2, "0")}/${d.getFullYear()}`;
};
</script>

<style scoped>
.card {
  box-shadow: 0 0.125rem 0.25rem rgba(0, 0, 0, 0.075);
}
.btn-primary {
  background-color: #17a2b8;
  border-color: #17a2b8;
}
.btn-primary:hover {
  background-color: #138496;
  border-color: #117a8b;
}
.badge {
  font-size: 0.9em;
  padding: 0.4em 0.6em;
}
.btn-close {
  font-size: 0.6em;
}
.border {
  border: 1px solid #ced4da;
  border-radius: 0.25rem;
  padding: 0.5rem;
  display: flex;
  flex-wrap: wrap;
}
</style>
