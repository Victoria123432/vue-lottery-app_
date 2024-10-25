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

function sortByName(users: User[]) {
  users.sort((a, b) => a.name.localeCompare(b.name));
}
function sortByDate(users: User[]) {
  users.sort((a, b) => {
    const dateA = a.date ? new Date(a.date).getTime() : new Date(0).getTime();
    const dateB = b.date ? new Date(b.date).getTime() : new Date(0).getTime();
    return dateA - dateB;
  });
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
          <th scope="col">
            <button
              class="btn btn-outline-secondary me-5 ms-5"
              @click.prevent="sortByName(users)"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                fill="currentColor"
                class="bi bi-sort-alpha-down"
                viewBox="0 0 16 16"
              >
                <path
                  fill-rule="evenodd"
                  d="M10.082 5.629 9.664 7H8.598l1.789-5.332h1.234L13.402 7h-1.12l-.419-1.371zm1.57-.785L11 2.687h-.047l-.652 2.157z"
                />
                <path
                  d="M12.96 14H9.028v-.691l2.579-3.72v-.054H9.098v-.867h3.785v.691l-2.567 3.72v.054h2.645zM4.5 2.5a.5.5 0 0 0-1 0v9.793l-1.146-1.147a.5.5 0 0 0-.708.708l2 1.999.007.007a.497.497 0 0 0 .7-.006l2-2a.5.5 0 0 0-.707-.708L4.5 12.293z"
                />
              </svg>
            </button>
            <button
              class="btn btn-outline-secondary"
              @click.prevent="sortByDate(users)"
            >
              <svg
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                fill="currentColor"
                class="bi bi-sort-numeric-down"
                viewBox="0 0 16 16"
              >
                <path
                  d="M12.438 1.668V7H11.39V2.684h-.051l-1.211.859v-.969l1.262-.906h1.046z"
                />
                <path
                  fill-rule="evenodd"
                  d="M11.36 14.098c-1.137 0-1.708-.657-1.762-1.278h1.004c.058.223.343.45.773.45.824 0 1.164-.829 1.133-1.856h-.059c-.148.39-.57.742-1.261.742-.91 0-1.72-.613-1.72-1.758 0-1.148.848-1.835 1.973-1.835 1.09 0 2.063.636 2.063 2.687 0 1.867-.723 2.848-2.145 2.848zm.062-2.735c.504 0 .933-.336.933-.972 0-.633-.398-1.008-.94-1.008-.52 0-.927.375-.927 1 0 .64.418.98.934.98"
                />
                <path
                  d="M4.5 2.5a.5.5 0 0 0-1 0v9.793l-1.146-1.147a.5.5 0 0 0-.708.708l2 1.999.007.007a.497.497 0 0 0 .7-.006l2-2a.5.5 0 0 0-.707-.708L4.5 12.293z"
                />
              </svg>
            </button>
          </th>
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
        data-bs-dismiss="modal"
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
