<script setup lang="ts">
const props = defineProps<{
  modelValue: Date | null | string;
  label: string;
  type: string;
  error: boolean;
  placeholder: string;
}>();

const emit = defineEmits<{
  (e: "update:modelValue", value: string | Date): void;
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
        'is-valid': !props.error && props.modelValue !== '',
      }"
    />
    <span v-if="props.error && props.modelValue === ''" class="text-danger"
      >This value is required</span
    >
  </div>
</template>
