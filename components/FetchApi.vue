<script setup>
import { ref, watch } from "vue";
const props = defineProps({
  url: {
    type: String,
    required: true,
  },
});

const data = ref(null);
const isLoading = ref(true);

watch(
  () => props.url,
  async () => {
    try {
      isLoading.value = true;
      const response = await fetch(props.url);
      data.value = await response.json();
    } finally {
      isLoading.value = false;
    }
  },
  { immediate: true }
);
</script>

<template>
  <slot v-bind="{ data, isLoading }" />
</template>
