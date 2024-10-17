<script setup lang="ts">
import { ref } from "vue";
import useVuelidate from "@vuelidate/core";
import { required, email, minLength } from "@vuelidate/validators";

interface User {
  name: string;
  date: Date | null;
  email: string;
  phone: string;
}

const newUser = ref<User>({
  name: "",
  date: null,
  email: "",
  phone: "",
});

const users = ref<User[]>([]);

const rules = {
  name: { required, minLength: minLength(2) },
  date: { required },
  email: { required, email },
  phone: { required },
};

const v$ = useVuelidate(rules, newUser);

function addUser() {
  v$.value.$touch();
  if (!v$.value.$pending && !v$.value.$invalid) {
    users.value.push({ ...newUser.value });

    newUser.value = {
      name: "",
      date: null,
      email: "",
      phone: "",
    };
    v$.value.$reset();
  }
}
</script>

<template>
  <div class="container">
    <form class="p-3 mb-5 bg-body-tertiary rounded form-style row">
      <div class="col-10">
        <input type="text" placeholder="Winners" class="form-control" />
      </div>
      <div class="col-auto">
        <button type="submit" class="btn btn-info">New winner</button>
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
            'is-invalid': v$.name.$error,
            'is-valid':
              !v$.name.required.$invalid && !v$.name.minLength.$invalid,
          }"
        />
        <span v-if="v$.name.$error" class="text-danger">Name is required</span>
      </div>

      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Date of Birth</label>
        <input
          v-model="newUser.date"
          type="date"
          class="form-control"
          :class="{ 'is-invalid': v$.date.$error }"
        />
        <span v-if="v$.email.$error" class="text-danger"
          >Birth day is required</span
        >
      </div>

      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Email</label>
        <input
          v-model="newUser.email"
          type="email"
          class="form-control"
          placeholder="Enter email"
          :class="{ 'is-invalid': v$.email.$error }"
        />
        <span v-if="v$.email.$error" class="text-danger"
          >A valid email is required</span
        >
      </div>

      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Phone number</label>
        <input
          v-model="newUser.phone"
          type="tel"
          pattern="\\(\d{3}\\)\\d{3}-\\d{4}"
          class="form-control"
          placeholder="Enter Phone number"
          :class="{ 'is-invalid': v$.phone.$error }"
        />
        <span v-if="v$.email.$error" class="text-danger"
          >Phone number is required</span
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
        <tbody>
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
