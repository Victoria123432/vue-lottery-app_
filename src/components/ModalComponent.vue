<script setup lang="ts">
import { defineEmits, defineProps, onMounted, onBeforeUnmount } from "vue";

const props = defineProps<{
  isOpen: boolean;
}>();

const emit = defineEmits<{
  (e: "ok"): void;
  (e: "close"): void;
}>();

const close = () => {
  emit("close");
};

const confirm = () => {
  emit("ok");
};

const handleKeydown = (e: KeyboardEvent) => {
  if (props.isOpen && e.key === "Escape") {
    close();
  }
};

onMounted(() => {
  document.addEventListener("keydown", handleKeydown);
});

onBeforeUnmount(() => {
  document.removeEventListener("keydown", handleKeydown);
});
</script>

<template>
  <div v-if="isOpen" class="backdrop" @click="close">
    <div class="popup" @click.stop>
      <h1 class="modal-title fs-5" id="exampleModalLabel">
        <slot name="title"></slot>
      </h1>
      <hr />
      <slot name="body"></slot>
      <hr />
      <div class="footer">
        <slot name="actions" :close="close" :confirm="confirm"></slot>
      </div>
    </div>
  </div>
</template>
