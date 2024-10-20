<script setup lang="ts">
import { User } from "./User";
const props = defineProps<{
  users: User[];
}>();
</script>

<template>
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
          v-for="({ name, date, email, phone }, index) in props.users"
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
</template>
