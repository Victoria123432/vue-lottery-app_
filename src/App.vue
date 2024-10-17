<script setup lang="ts">
import { ref } from "vue";
interface User {
  name: string;
  date: Date | null;
  email: string;
  phone: string;
}

const userName = ref<string>("");
const userDateOfBirth = ref<Date | null>(null);
const userEmail = ref<string>("");
const userPhoneNumber = ref<string>("");

const users = ref<User[]>([]);

function addUser() {
  users.value.push({
    name: userName.value,
    date: userDateOfBirth.value,
    email: userEmail.value,
    phone: userPhoneNumber.value,
  });

  userName.value = "";
  userDateOfBirth.value = null;
  userEmail.value = "";
  userPhoneNumber.value = "";
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
          v-model="userName"
          type="text"
          class="form-control"
          placeholder="Enter user name"
        />
      </div>
      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Date of Birth</label>
        <input v-model="userDateOfBirth" type="date" class="form-control" />
      </div>
      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Email</label>
        <input
          v-model="userEmail"
          type="email"
          class="form-control"
          placeholder="Enter email"
        />
      </div>
      <div class="mb-3">
        <label class="form-label fs-5 fw-bold">Phone number</label>
        <input
          v-model="userPhoneNumber"
          type="tel"
          pattern="\\(\d{3}\\)\\d{3}-\\d{4}"
          class="form-control"
          placeholder="Enter Phone number"
        />
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
