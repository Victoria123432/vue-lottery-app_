<script setup lang="ts">
const emit = defineEmits<{
  (e: "filter-by-name", value: string): void;
}>();
function updateValue(event: Event) {
  const target = event.target as HTMLInputElement;
  emit("filter-by-name", target.value);
}
</script>

<template>
  <link
    href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css"
    rel="stylesheet"
  />
  <div class="container">
    <div class="row justify-content-center">
      <div class="col-md-6">
        <div class="search-container">
          <input
            @input="updateValue"
            type="text"
            class="form-control search-input"
            placeholder="Search..."
          />
          <i class="fas fa-search search-icon"></i>
        </div>
      </div>
    </div>
  </div>
</template>
