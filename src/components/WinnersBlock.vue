<script setup lang="ts">
import { ref, computed } from "vue";
import { User } from "./User";
import WinnerComponent from "./WinnerComponent.vue";

const winners = ref<User[]>([]);

const props = defineProps<{
  users: User[];
}>();

const isDisabled = computed(
  () => winners.value.length >= 3 || props.users.length === 0,
);

function newWinner() {
  let winner;
  do {
    const num: number = Math.floor(Math.random() * props.users.length);
    winner = props.users[num];
  } while (winners.value.includes(winner));
  winners.value.push(winner);
}

function removeWinner(index: number) {
  winners.value.splice(index, 1);
}
</script>

<template>
  <form class="bg-body-tertiary rounded form-style row align-items-center">
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
      <ButtonComponent
        :disabled="isDisabled"
        label="New Winner"
        @click="newWinner"
      />
    </div>
  </form>
</template>
<style lang="scss">
@import "./scss/styles.scss";
</style>
