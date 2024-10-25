<script setup lang="ts">
import { ref, watch, onMounted } from "vue";
import { User } from "./components/User";
import ParticipantsTable from "./components/ParticipantsTable.vue";
import RegistrationForm from "./components/RegistrationForm.vue";
import WinnersBlock from "./components/WinnersBlock.vue";
import ButtonComponent from "./components/ButtonComponent.vue";

const users = ref<User[]>([]);

function addUser(user: User) {
  const maxId = users.value.reduce(
    (max, current) => (current.id > max ? current.id : max),
    0,
  );
  user.id = maxId + 1;
  users.value.push(user);
}
// const newUser: User;

// function addUser() {
//   if (!v$.value.$pending && !v$.value.$invalid) {
//     const maxId = users.value.reduce(
//       (max, current) => (current.id > max ? current.id : max),
//       0,
//     );

//     newUser.id = maxId + 1;
//     users.value.push(newUser);

//     newUser.name = "";
//     newUser.date = null;
//     newUser.email = "";
//     newUser.phone = "";

//     v$.value.$reset();
//   }
// }
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

// function updateUsers(updatedUsers: User[]) {
//   users.value = [...updatedUsers];
// }
</script>

<template>
  <div class="container"></div>
  <WinnersBlock :users="users" />
  <RegistrationForm @new-user="addUser">
    <template v-slot:header>
      <p class="text-capitalize fs-3 fw-bold">Register form</p>
      <p class="fs-5 light-grey-text">Please fill in all the fields.</p>
    </template>
    <template v-slot:footer>
      <div class="right-btn">
        <ButtonComponent label="Save" :disabled="false" />
      </div>
    </template>
  </RegistrationForm>
  <ParticipantsTable :users="users" />
</template>

<style lang="scss">
@import "./scss/styles.scss";
</style>
