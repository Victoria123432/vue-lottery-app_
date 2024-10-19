<script setup lang="ts">
const props = defineProps<{
  modelValue: string | Date | null;
  label: string;
  type: string;
  error: boolean;
  errorMessage: string;
  placeholder: string;
}>();

const emit = defineEmits<{
  (e: "update:modelValue", value: string): void;
}>();

function updateValue(event: Event) {
  const target = event.target as HTMLInputElement;
  emit("update:modelValue", target.value);
}
</script>

<template>
  <div class="mb-3">
    <label class="form-label fs-5 fw-bold">{{ props.label }}</label>
    <input
      :value="props.modelValue"
      @input="updateValue"
      :type="props.type"
      class="form-control"
      :placeholder="props.placeholder"
      :class="{
        'is-invalid': props.error,
        'is-valid': !props.error,
      }"
    />
    <span v-if="props.error" class="text-danger">{{ props.errorMessage }}</span>
  </div>
</template>
<style lang="scss">
@import "./scss/styles.scss";
</style>
