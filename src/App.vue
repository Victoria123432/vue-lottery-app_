<script setup lang="ts">
import { ref, reactive, computed } from "vue";
import useVuelidate from "@vuelidate/core";
import { required, email, minLength, helpers } from "@vuelidate/validators";
import moment from "moment";

const d = new Date();

const mustBeValidDate = helpers.withParams(
  { type: "mustBeValidDate" },
  (value: string) =>
    !helpers.req(value) ||
    moment(value) <=
      moment(new Date(d.getFullYear() - 14, d.getMonth(), d.getDay())),
);

interface User {
  name: string;
  date: Date | null;
  email: string;
  phone: string;
}

const newUser = reactive<User>({
  name: "",
  date: null,
  email: "",
  phone: "",
});
const focusedFields = reactive({
  name: false,
  date: false,
  email: false,
  phone: false,
});

const users = ref<User[]>([]);
const winners = ref<User[]>([]);

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
    users.value.push({ ...newUser });

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

function newWinner() {
  let winner;
  do {
    const num: number = Math.floor(Math.random() * users.value.length);
    winner = users.value[num];
  } while (winners.value.includes(winner));
  winners.value.push(winner);
}

function deleteWinner(index: number) {
  winners.value.splice(index, 1);
}

const isDisabled = computed(
  () =>
    winners.value.length >= 3 ||
    users.value.length === 0 ||
    winners.value.length === users.value.length,
);
</script>

<template>
  <div class="container">
    <form class="bg-body-tertiary rounded form-style row align-items-center">
      <div
        class="card border-secondary mb-3 light-grey-text col-sm-10 mb-3 mb-sm-0 d-flex align-items-center"
      >
        <div class="p-3 d-flex align-items-center">
          <span class="fw-bold me-3">Winners:</span>
          <div>
            <span
              v-for="(winner, index) in winners"
              :key="index"
              class="p-2 bg-info text-white rounded me-2"
            >
              {{ winner.name }}
              <button
                class="btn btn-info btn-sm ms-2"
                @click.prevent="deleteWinner(index)"
              >
                x
              </button>
            </span>
          </div>
        </div>
      </div>
      <div class="col-auto">
        <button
          @click.prevent="newWinner"
          class="btn btn-info"
          :disabled="isDisabled"
        >
          New winner
        </button>
      </div>
    </form>

    <form class="p-3 mb-5 bg-body-tertiary rounded form-style">
      <p class="text-capitalize fs-3 fw-bold">Register form</p>
      <p class="fs-5 light-grey-text">Please fill in all the fields.</p>

      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Name</label>
        <input
          v-model="newUser.name"
          type="text"
          class="form-control"
          placeholder="Enter user name"
          :class="{
            'is-invalid': v$.name.minLength.$invalid,
            'is-valid':
              !v$.name.required.$invalid && !v$.name.minLength.$invalid,
          }"
          @focus="focusedFields.name = true"
          @blur="focusedFields.name = false"
        />
        <span
          v-if="v$.name.required.$invalid && focusedFields.name"
          class="text-danger"
          >This value is required</span
        >
        <span v-if="v$.name.minLength.$invalid" class="text-danger"
          >Name should have at least two letters</span
        >
      </div>

      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Date of Birth</label>
        <input
          v-model="newUser.date"
          type="date"
          class="form-control"
          :class="{
            'is-invalid': v$.date.mustBeValidDate.$invalid,
            'is-valid':
              !v$.date.required.$invalid && !v$.date.mustBeValidDate.$invalid,
          }"
          @focus="!!focusedFields.date"
          @blur="!focusedFields.date"
        />
        <span
          v-if="v$.date.required.$invalid && focusedFields.date"
          class="text-danger"
        >
          This value is required
        </span>
        <span
          v-if="!v$.date.required.$invalid && v$.date.mustBeValidDate.$invalid"
          class="text-danger"
        >
          You must reach 14
        </span>
      </div>

      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Email</label>
        <input
          v-model="newUser.email"
          type="email"
          class="form-control"
          placeholder="Enter email"
          :class="{
            'is-invalid': v$.email.email.$invalid,
            'is-valid': !v$.email.email.$invalid && !v$.email.required.$invalid,
          }"
          @focus="focusedFields.email = true"
          @blur="focusedFields.email = false"
        />
        <span
          v-if="v$.email.required.$invalid && focusedFields.email"
          class="text-danger"
          >This value is required</span
        >
        <span
          v-if="!v$.email.required.$invalid && v$.email.email.$invalid"
          class="text-danger"
          >Email should have @</span
        >
      </div>

      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Phone number</label>
        <input
          v-model="newUser.phone"
          @input="formatPhoneNumber()"
          type="tel"
          class="form-control"
          placeholder="Enter Phone number"
          :class="{
            'is-invalid': v$.phone.phv.$invalid,
            'is-valid': !v$.phone.required.$invalid && !v$.phone.phv.$invalid,
          }"
          @focus="focusedFields.phone = true"
          @blur="focusedFields.phone = false"
        />
        <span
          v-if="v$.phone.required.$invalid && focusedFields.phone"
          class="text-danger"
          >This value is required</span
        >
        <span
          v-if="!v$.phone.required.$invalid && v$.phone.phv.$invalid"
          class="text-danger"
          >Phone number should have 10 numbers</span
        >
      </div>

      <div class="right-btn">
        <button @click.prevent="addUser" class="btn btn-info">Save</button>
      </div>
    </form>

    <form class="p-3 mb-5 bg-body-tertiary rounded form-style">
      <table class="table">
        <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Name</th>
            <th scope="col">Date of Birth</th>
            <th scope="col">Email</th>
            <th scope="col">Phone number</th>
          </tr>
        </thead>
        <tbody class="light-grey-text">
          <tr
            v-for="({ name, date, email, phone }, index) in users"
            :key="index"
          >
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ name }}</td>
            <td>{{ date }}</td>
            <td>{{ email }}</td>
            <td>{{ phone }}</td>
          </tr>
        </tbody>
      </table>
    </form>
  </div>
</template>

<style lang="scss">
@import "./scss/styles.scss";
</style>
