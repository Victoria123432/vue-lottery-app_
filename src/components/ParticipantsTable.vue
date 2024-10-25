<script setup lang="ts">
import { User } from "./User";
import { ref, computed } from "vue";
import ButtonComponent from "./ButtonComponent.vue";
import RegistrationForm from "./RegistrationForm.vue";
import { Modal } from "bootstrap";
import ModalComponent from "./ModalComponent.vue";
const props = defineProps<{
  users: User[];
}>();

const localUsers = computed(() => props.users);

const selectedUser = ref<User | null>(null);

const openEditModal = (user: User) => {
  selectedUser.value = { ...user };
  const modalElement = document.getElementById("exampleModal") as HTMLElement;
  console.log(modalElement);
  if (modalElement) {
    const modal = new Modal(modalElement);
    modal.show();
  } else {
    console.error("Modal element not found");
  }
};

const emit = defineEmits<{
  (e: "update-users", users: User[]): void;
}>();

function handleSaveChanges(updatedUser: User) {
  const index = props.users.findIndex((user) => user.id === updatedUser.id);
  if (index !== -1) {
    localUsers.value[index] = { ...updatedUser };

    emit("update-users", localUsers.value);

    const modalElement = document.getElementById("exampleModal") as HTMLElement;
    const modal = Modal.getInstance(modalElement);
    modal?.hide();
  }
}
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
          <th scope="col"></th>
        </tr>
      </thead>
      <tbody class="light-grey-text">
        <tr v-for="(user, index) in localUsers" :key="index">
          <th scope="row">{{ index + 1 }}</th>
          <td>{{ user.name }}</td>
          <td>{{ user.date }}</td>
          <td>{{ user.email }}</td>
          <td>{{ user.phone }}</td>
          <td>
            <ButtonComponent
              label="Edit"
              :disabled="false"
              @click="openEditModal(user)"
            />
            <ButtonComponent
              label="Delate"
              :disabled="false"
              class="btn btn-danger"
            />
          </td>
        </tr>
      </tbody>
    </table>
  </form>

  <ModalComponent v-if="selectedUser" id="exampleModal">
    <template v-slot:header> Editing {{ selectedUser?.name }} </template>
    <template v-slot:body>
      <RegistrationForm @new-user="handleSaveChanges" />
    </template>
    <template v-slot:footer>
      <ButtonComponent
        label="Save changes"
        :disabled="false"
        @click="handleSaveChanges(selectedUser)"
      ></ButtonComponent>
    </template>
  </ModalComponent>
</template>
