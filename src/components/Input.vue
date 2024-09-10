<template>
  <div class="flex flex-col gap-y-2">
    <label :for="id">{{ label }}</label>
    <input
      class="rounded-md border border-gray-300"
      :value="value"
      :type="type"
      :id="id"
      :placeholder="placeholder"
      v-model="value"
    />
  </div>
</template>
<script lang="ts" setup>
import { defineProps, defineModel } from "vue";

const value = defineModel();

const props = defineProps({
  id: String,
  label: String,
  placeholder: String,
  type: {
    type: String as () =>
      | "text"
      | "number"
      | "email"
      | "password"
      | "tel"
      | "url",
    default: "text",
  },
});
</script>
