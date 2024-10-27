<!-- EditParticipantForm.vue -->
<template>
  <form @submit.prevent="submitForm">
    <h2>Edit Participant</h2>
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
    <CustomButton type="submit" variant="primary">Update</CustomButton>
  </form>
</template>

<script setup lang="ts">
import { reactive } from "vue";
import CustomInput from "./CustomInput.vue";
import CustomButton from "./CustomButton.vue";

const props = defineProps<{
  participant: {
    id: string;
    name: string;
    dob: string;
    email: string;
    phone: string;
  };
}>();

const form = reactive({ ...props.participant });

const errors = reactive({
  name: "",
  dob: "",
  email: "",
  phone: "",
});

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
  (e: "update-participant", participant: typeof form): void;
}>();

const submitForm = () => {
  if (validateForm()) {
    emit("update-participant", { ...form });
  }
};
</script>
