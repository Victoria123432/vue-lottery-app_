<script setup lang="ts">
// import { ref } from "vue";

const props = defineProps<{
  label: string;
  disabled: boolean;
  onClick: () => void;
}>();
</script>

<template>
  <button
    @click.prevent="props.onClick"
    :disabled="props.disabled"
    class="btn btn-info"
  >
    {{ props.label }}
  </button>
</template>

<style lang="scss">
@import "./scss/styles.scss";
</style>
