<script setup lang="ts">
import { ref, computed, watch, onMounted } from "vue";
import { User } from "./User";
import WinnerComponent from "./WinnerComponent.vue";
import ButtonComponent from "./ButtonComponent.vue";

const winners = ref<User[]>([]);

const props = defineProps<{
  users: User[];
}>();

const isDisabled = computed(
  () =>
    winners.value.length >= 3 ||
    props.users.length === 0 ||
    winners.value.length === props.users.length,
);

function newWinner() {
  let winner;
  do {
    const num: number = Math.floor(Math.random() * props.users.length);
    winner = props.users[num];
  } while (winners.value.includes(winner));
  winners.value.push(winner);
}

function removeWinner(id: number) {
  const index = winners.value.findIndex((winner) => winner.id === id);
  if (index !== -1) {
    winners.value.splice(index, 1);
  }
}

watch(
  winners,
  (newVal) => {
    localStorage.setItem("winners", JSON.stringify(newVal));
  },
  { deep: true },
);

onMounted(() => {
  const storedUsers = localStorage.getItem("winners");
  winners.value = storedUsers ? JSON.parse(storedUsers) : [];
});
</script>

<template>
  <form
    @submit.prevent="newWinner"
    class="bg-body-tertiary rounded form-style row align-items-center"
  >
    <div
      class="card border-secondary mb-3 light-grey-text col-sm-10 mb-3 mb-sm-0 d-flex align-items-center"
    >
      <div class="p-3 d-flex align-items-center">
        <span class="fw-bold me-3">Winners:</span>
        <div>
          <WinnerComponent :winners="winners" @removeWinner="removeWinner" />
        </div>
      </div>
    </div>
    <div class="col-auto">
      <ButtonComponent :disabled="isDisabled" label="New Winner" />
    </div>
  </form>
</template>
