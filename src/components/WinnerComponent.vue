<script setup lang="ts">
import { User } from "./User.ts";
const props = defineProps<{
  winners: User[];
}>();

const emit = defineEmits<{
  (e: "removeWinner", index: number): void;
}>();

function deleteWinner(index: number) {
  emit("removeWinner", index);
}
</script>

<template>
  <span
    v-for="(winner, index) in props.winners"
    :key="index"
    class="p-2 bg-info text-white rounded me-2"
  >
    {{ winner.name }}
    <button
      class="btn btn-info btn-sm ms-2"
      @click.prevent="deleteWinner(index)"
    >
      x
    </button>
  </span>
</template>

<style lang="scss">
@import "./scss/styles.scss";
</style>
