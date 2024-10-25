<script setup lang="ts">
import { User } from "./User";
import { ref, nextTick } from "vue";
import ButtonComponent from "./ButtonComponent.vue";
import RegistrationForm from "./RegistrationForm.vue";
import { Modal } from "bootstrap";
import ModalComponent from "./ModalComponent.vue";

const props = defineProps<{
  users: User[];
}>();

const selectedUser = ref<User | null>(null);

const openEditModal = async (user: User) => {
  selectedUser.value = { ...user };
  await nextTick();
  const modalElement = document.getElementById("editModal") as HTMLElement;
  if (modalElement) {
    const modal = new Modal(modalElement);
    modal.show();
  } else {
    console.error("Modal element not found");
  }
};
const OpenDeleteModal = async (user: User) => {
  selectedUser.value = { ...user };
  await nextTick();
  const modalElement = document.getElementById("deleteModal") as HTMLElement;
  if (modalElement) {
    const modal = new Modal(modalElement);
    modal.show();
  } else {
    console.error("Modal element not found");
  }
};

const emit = defineEmits<{
  (e: "update-users", users: User): void;
  (e: "delete-user", id: number): void;
}>();

function closeModal(newUserData: User) {
  selectedUser.value = newUserData;
  if (selectedUser.value) {
    selectedUser.value = null;

    const modalElement = document.getElementById("editModal") as HTMLElement;
    const modal = Modal.getInstance(modalElement);
    modal?.hide();
  }
}

function saveChanges(newUserData: User) {
  selectedUser.value = newUserData;
  if (selectedUser.value) {
    emit("update-users", selectedUser.value);
    closeModal(selectedUser.value);
  }
}
function deleteUser(id: number) {
  emit("delete-user", id);
  const modalElement = document.getElementById("deleteModal") as HTMLElement;
  const modal = Modal.getInstance(modalElement);
  modal?.hide();
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
        <tr v-for="(user, index) in props.users" :key="index">
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
              @click="OpenDeleteModal(user)"
            />
          </td>
        </tr>
      </tbody>
    </table>
  </form>

  <ModalComponent
    v-if="selectedUser"
    id="editModal"
    @keyup.esc="closeModal(selectedUser)"
  >
    <template v-slot:title> Editing {{ selectedUser?.name }} </template>
    <template v-slot:close-button>
      <button
        type="button"
        class="btn-close"
        @click="closeModal(selectedUser)"
        aria-label="Close"
      ></button>
    </template>
    <template v-slot:body>
      <RegistrationForm :user="selectedUser" @new-user="saveChanges">
        <template v-slot:footer="{ submitForm }">
          <div class="right-btn">
            <ButtonComponent
              label="Save changes"
              :disabled="false"
              @click="submitForm"
            />
          </div>
        </template>
      </RegistrationForm>
    </template>
  </ModalComponent>
  <ModalComponent v-if="selectedUser" id="deleteModal">
    <template v-slot:title> Delating {{ selectedUser?.name }} </template>
    <template v-slot:close-button>
      <button
        type="button"
        class="btn-close"
        @click="closeModal(selectedUser)"
        aria-label="Close"
      ></button>
    </template>
    <template v-slot:body>
      Are you shure you want to delate {{ selectedUser?.name }},
      {{ selectedUser?.email }}
    </template>
    <template v-slot:footer>
      <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">
        Close
      </button>
      <button
        type="button"
        class="btn btn-danger"
        @click="deleteUser(selectedUser.id)"
      >
        Delete
      </button>
    </template>
  </ModalComponent>
</template>
