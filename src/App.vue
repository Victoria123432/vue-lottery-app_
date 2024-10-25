<script setup lang="ts">
import { ref, watch, onMounted, computed } from "vue";
import { User } from "./components/User";
import ParticipantsTable from "./components/ParticipantsTable.vue";
import RegistrationForm from "./components/RegistrationForm.vue";
import WinnersBlock from "./components/WinnersBlock.vue";
import ButtonComponent from "./components/ButtonComponent.vue";
import SearchBar from "./components/SearchBar.vue";

const users = ref<User[]>([]);
const searchUser = ref("");

function addUser(user: User) {
  const maxId = users.value.reduce(
    (max, current) => (current.id > max ? current.id : max),
    0,
  );
  user.id = maxId + 1;
  users.value.push(user);
}

function updateUsers(updatedUser: User) {
  const index = users.value.findIndex((user) => user.id === updatedUser.id);
  if (index !== -1) {
    users.value[index] = { ...updatedUser };
  }
}

function deleteUser(id: number) {
  const index = users.value.findIndex((user) => user.id === id);
  if (index !== -1) {
    users.value.splice(index, 1);
  }
}

const filteredUsers = computed(() => {
  if (!searchUser.value) return users.value;
  return users.value.filter((user) =>
    user.name
      .toLocaleLowerCase()
      .includes(searchUser.value.toLocaleLowerCase()),
  );
});

watch(
  users,
  (newVal) => {
    localStorage.setItem("users", JSON.stringify(newVal));
  },
  { deep: true },
);

onMounted(() => {
  const storedUsers = localStorage.getItem("users");
  users.value = storedUsers ? JSON.parse(storedUsers) : [];
});
</script>

<template>
  <div class="container"></div>
  <WinnersBlock :users="users" />
  <RegistrationForm :users="users" @new-user="addUser">
    <template v-slot:header>
      <p class="text-capitalize fs-3 fw-bold">Register form</p>
      <p class="fs-5 light-grey-text">Please fill in all the fields.</p>
    </template>
    <template v-slot:footer="{ submitForm }">
      <div class="right-btn">
        <ButtonComponent label="Save" :disabled="false" @click="submitForm" />
      </div>
    </template>
  </RegistrationForm>
  <SearchBar @filter-by-name="searchUser = $event"></SearchBar>
  <ParticipantsTable
    :users="filteredUsers"
    @update-users="updateUsers"
    @delete-user="deleteUser"
  />
</template>

<style lang="scss">
@import "./scss/styles.scss";
</style>
