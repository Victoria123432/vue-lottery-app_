<script setup lang="ts">
import { reactive } from "vue";
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

// const props = defineProps<{
//   onAddUser: (user: User) => void;
// }>();

const emit = defineEmits<{
  (e: "user-added", user: User): void;
}>();

const newUser = reactive<User>({
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
  v$.value.$touch();
  if (!v$.value.$pending && !v$.value.$invalid) {
    emit("user-added", { ...newUser }); // Emit the event with user data
    newUser.name = "";
    newUser.date = null;
    newUser.email = "";
    newUser.phone = "";
  }
}
</script>

<template>
  <form class="p-3 mb-5 bg-body-tertiary rounded form-style">
    <p class="text-capitalize fs-3 fw-bold">Register form</p>
    <p class="fs-5 light-grey-text">Please fill in all the fields.</p>

    <InputField
      v-model="newUser.name"
      label="Name"
      type="text"
      :error="v$.name.$error"
      errorMessage="This value is required"
      placeholder="Enter user name"
    />

    <InputField
      v-model="newUser.date"
      label="Date of Birth"
      type="date"
      :error="v$.date.$error"
      errorMessage="This value is required"
      placeholder="Select date"
    />
    <span v-if="v$.date.$error" class="text-danger"
      >This value is required</span
    >

    <InputField
      v-model="newUser.email"
      label="Email"
      type="email"
      :error="v$.email.$error"
      errorMessage="This value is required"
      placeholder="Enter email"
    />

    <InputField
      v-model="newUser.phone"
      label="Phone number"
      type="tel"
      :error="v$.phone.$error"
      errorMessage="This value is required"
      placeholder="Enter Phone number"
    />

    <div class="right-btn">
      <ButtonComponent label="Save" :disabled="false" @click="addUser" />
    </div>
  </form>
</template>
<style lang="scss">
@import "./scss/styles.scss";
</style>
