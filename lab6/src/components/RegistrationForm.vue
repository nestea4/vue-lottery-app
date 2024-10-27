<!-- RegistrationForm.vue -->
<template>
  <div class="card mb-4">
    <div class="card-body">
      <h5 class="card-title">REGISTER FORM</h5>
      <p class="text-muted">Please fill in all the fields.</p>
      <form @submit.prevent="submitForm" @keydown.enter.prevent="submitForm">
        <CustomInput
          v-model="form.name"
          type="text"
          placeholder="Enter user name"
          :error="errors.name"
          @blur="validateField('name')"
        />
        <CustomInput
          v-model="form.dob"
          type="date"
          :error="errors.dob"
          @blur="validateField('dob')"
        />
        <CustomInput
          v-model="form.email"
          type="email"
          placeholder="Enter email"
          :error="errors.email"
          @blur="validateField('email')"
        />
        <CustomInput
          v-model="form.phone"
          type="tel"
          placeholder="Enter Phone number"
          :error="errors.phone"
          @blur="validateField('phone')"
        />
        <CustomButton type="submit" variant="primary">
          <i class="bi bi-person-plus"></i> Save
        </CustomButton>
      </form>
    </div>
  </div>
  <Modal v-if="showErrorModal" @close="closeErrorModal">
    <div class="modal-header">
      <h5 class="modal-title">Error</h5>
      <!-- <button type="button" class="btn-close" @click="closeErrorModal"></button> -->
    </div>
    <div class="modal-body">
      <p>{{ errorMessage }}</p>
    </div>
    <div class="modal-footer">
      <CustomButton @click="closeErrorModal" variant="primary"
        >Close</CustomButton
      >
    </div>
  </Modal>
</template>

<script setup lang="ts">
import { reactive, ref } from "vue";
import CustomInput from "./CustomInput.vue";
import CustomButton from "./CustomButton.vue";
import Modal from "./Modal.vue";

const form = reactive({
  name: "",
  dob: "",
  email: "",
  phone: "",
});

const errors = reactive({
  name: "",
  dob: "",
  email: "",
  phone: "",
});

const showErrorModal = ref(false);
const errorMessage = ref("");

const validateField = (field: keyof typeof form) => {
  errors[field] = "";
  switch (field) {
    case "name":
      if (!form.name.trim()) errors.name = "Name is required";
      break;
    case "dob":
      if (!form.dob) {
        errors.dob = "Date of Birth is required";
      } else if (new Date(form.dob) > new Date()) {
        errors.dob = "Date of Birth cannot be in the future";
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
  (Object.keys(form) as Array<keyof typeof form>).forEach((field) => {
    validateField(field);
    if (errors[field]) isValid = false;
  });
  return isValid;
};

const emit = defineEmits<{
  (e: "register-participant", participant: typeof form): void;
}>();

const submitForm = () => {
  if (validateForm()) {
    emit("register-participant", { ...form });
    resetForm();
  }
};

const resetForm = () => {
  Object.keys(form).forEach((key) => (form[key as keyof typeof form] = ""));
  Object.keys(errors).forEach(
    (key) => (errors[key as keyof typeof errors] = "")
  );
};

const showEmailExistsError = () => {
  errorMessage.value = "A participant with this email already exists.";
  showErrorModal.value = true;
};

const closeErrorModal = () => {
  showErrorModal.value = false;
  errorMessage.value = "";
};

defineExpose({ showEmailExistsError });
</script>
