<script setup lang="ts">
const props = defineProps<{
  label: string;
  disabled: boolean;
}>();

const emit = defineEmits<{
  (e: "click"): void; // Allow optional parameter
}>();
</script>

<template>
  <button
    @click="emit('click')"
    :disabled="props.disabled"
    class="btn btn-info"
    type="button"
  >
    {{ props.label }}
  </button>
</template>
