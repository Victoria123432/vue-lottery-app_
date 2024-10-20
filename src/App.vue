<script setup lang="ts">
import { ref } from "vue";
import { User } from "./components/User";
import ParticipantsTable from "./components/ParticipantsTable.vue";
import RegistrationForm from "./components/RegistrationForm.vue";
import WinnersBlock from "./components/WinnersBlock.vue";

const users = ref<User[]>([]);

function addUser(user: User) {
  const maxId = users.value.reduce(
    (max, current) => (current.id > max ? current.id : max),
    0,
  );
  user.id = maxId + 1;
  users.value.push(user);
}
</script>

<template>
  <div class="container"></div>
  <WinnersBlock :users="users" />
  <RegistrationForm @user-added="addUser" />
  <ParticipantsTable :users="users" />
</template>

<style lang="scss">
@import "./scss/styles.scss";
</style>
