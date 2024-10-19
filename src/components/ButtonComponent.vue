<script setup lang="ts">
// import { ref } from "vue";

const props = defineProps<{
  label: string;
  disabled: boolean;
}>();

const emit = defineEmits<{
  (e: "click"): void; // Correctly define the click event
}>();
</script>

<template>
  <button
    @click="emit('click')"
    :disabled="props.disabled"
    class="btn btn-info"
  >
    {{ props.label }}
  </button>
</template>

<style lang="scss">
@import "./scss/styles.scss";
</style>
