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
  <!-- <div
    class="modal fade"
    id="editModal"
    tabindex="-1"
    aria-labelledby="exampleModalLabel"
    aria-hidden="true"
  >
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h1 class="modal-title fs-5" id="exampleModalLabel">
            <slot name="title"></slot>
          </h1>
          <slot name="close-button"></slot>
        </div>
        <div class="modal-body">
          <slot name="body"></slot>
        </div>
        <div class="modal-footer">
          <slot name="footer"></slot>
        </div>
      </div>
    </div>
  </div> -->

  <div v-if="isOpen" class="backdrop" @click="close">
    <div class="popup" @click.stop>
      <h1 class="modal-title fs-5" id="exampleModalLabel">
        <slot name="title"></slot>
      </h1>
      <hr />
      <slot name="body"></slot>
      <hr />
      <div class="footer">
        <slot name="actions" :close="close" :confirm="confirm">
          <button @click="close">Cancel</button>
          &nbsp;
          <button @click="confirm">Ok</button>
        </slot>
      </div>
    </div>
  </div>
</template>
