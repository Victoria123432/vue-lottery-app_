<script setup lang="ts">
import { ref } from "vue";

const focused = ref();

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
        'is-invalid':
          props.error && props.modelValue !== '' && props.modelValue !== null,
        'is-valid':
          !props.error && props.modelValue !== '' && props.modelValue !== null,
      }"
      @focus="focused"
      @blur="!focused"
    />
    <span
      v-if="(props.modelValue === '' || props.modelValue === null) && focused"
      class="text-danger"
      >This value is required</span
    >
  </div>
</template>
