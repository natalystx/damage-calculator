<template>
  <div>
    <div class="flex flex-col gap-y-2">
      <p class="text-base font-bold">{{ label }}</p>
      <p class="text-base">
        {{ value }}
      </p>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { defineProps } from "vue";

defineProps({
  label: String,
  value: [Number, String],
});
</script>
