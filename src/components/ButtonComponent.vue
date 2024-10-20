<script setup lang="ts">
const props = defineProps<{
  label: string;
  disabled: boolean;
}>();

const emit = defineEmits<{
  (e: "click"): void;
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
