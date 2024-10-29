<script setup lang="ts">
import { User } from "../types/User.ts";
const props = defineProps<{
  winners: User[];
}>();

const emit = defineEmits<{
  (e: "removeWinner", id: number): void;
}>();

function deleteWinner(id: number) {
  emit("removeWinner", id);
}
</script>

<template>
  <span
    v-for="winner in props.winners"
    :key="winner.id"
    class="p-2 bg-info text-white rounded me-2"
  >
    {{ winner.name }}
    <button class="btn btn-info btn-sm ms-2" @click="deleteWinner(winner.id)">
      x
    </button>
  </span>
</template>
