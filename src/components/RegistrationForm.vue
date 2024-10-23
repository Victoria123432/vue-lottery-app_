<script setup lang="ts">
import { reactive, watch } from "vue";
import useVuelidate from "@vuelidate/core";
import { required, email, minLength, helpers } from "@vuelidate/validators";
import InputField from "./InputField.vue";
import ButtonComponent from "./ButtonComponent.vue";
import { User } from "./User";
import moment from "moment";

const d = new Date();

const mustBeValidDate = helpers.withParams(
  { type: "mustBeValidDate" },
  (value: string) =>
    !helpers.req(value) ||
    moment(value) <=
      moment(new Date(d.getFullYear() - 14, d.getMonth(), d.getDay())),
);

const emit = defineEmits<{
  (e: "user-added", user: User): void;
}>();

const newUser = reactive<User>({
  id: 0,
  name: "",
  date: null,
  email: "",
  phone: "",
});

const rules = {
  name: { required, minLength: minLength(2) },
  date: {
    required,
    mustBeValidDate,
  },
  email: { required, email },
  phone: { required, phv: helpers.regex(/^\(\d{3}\)\s?\d{3}-\d{4}$/) },
};

const v$ = useVuelidate(rules, newUser);

function addUser() {
  if (!v$.value.$pending && !v$.value.$invalid) {
    emit("user-added", { ...newUser });

    newUser.name = "";
    newUser.date = null;
    newUser.email = "";
    newUser.phone = "";

    v$.value.$reset();
  }
}

function formatPhoneNumber() {
  if (!newUser.phone) return newUser.phone;

  const phoneNumber = newUser.phone.replace(/[^\d]/g, "");

  if (phoneNumber.length <= 3) {
    newUser.phone = `(${phoneNumber}`;
  } else if (phoneNumber.length <= 6) {
    newUser.phone = `(${phoneNumber.slice(0, 3)}) ${phoneNumber.slice(3)}`;
  } else {
    newUser.phone = `(${phoneNumber.slice(0, 3)}) ${phoneNumber.slice(3, 6)}-${phoneNumber.slice(6, 10)}`;
  }
}

watch(newUser, () => {
  v$.value.$touch();
});
</script>

<template>
  <form
    @submit.prevent="addUser"
    class="p-3 mb-5 bg-body-tertiary rounded form-style"
    @keyup.enter="addUser"
  >
    <p class="text-capitalize fs-3 fw-bold">Register form</p>
    <p class="fs-5 light-grey-text">Please fill in all the fields.</p>

    <InputField
      v-model="newUser.name"
      label="Name"
      type="text"
      :error="v$.name.$error"
      placeholder="Enter user name"
    />
    <span
      v-if="!v$.name.required.$invalid && v$.name.minLength.$invalid"
      class="text-danger"
      >Name should have at least two letters</span
    >

    <InputField
      v-model="newUser.date"
      label="Date of Birth"
      type="date"
      :error="v$.date.$error"
      placeholder="Select date"
    />
    <span
      v-if="!v$.date.required.$invalid && v$.date.mustBeValidDate.$invalid"
      class="text-danger"
    >
      You must reach 14
    </span>

    <InputField
      v-model="newUser.email"
      label="Email"
      type="email"
      :error="v$.email.$error"
      placeholder="Enter email"
    />
    <span
      v-if="!v$.email.required.$invalid && v$.email.$error"
      class="text-danger"
      >Email should have @</span
    >

    <InputField
      v-model="newUser.phone"
      @input="formatPhoneNumber"
      label="Phone number"
      type="tel"
      :error="v$.phone.$error"
      placeholder="Enter Phone number"
    />
    <span
      v-if="!v$.phone.required.$invalid && v$.phone.phv.$invalid"
      class="text-danger"
      >Phone number should have 10 numbers</span
    >

    <div class="right-btn">
      <ButtonComponent label="Save" :disabled="false" @click="addUser" />
    </div>
  </form>
</template>
