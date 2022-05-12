<script setup>
defineProps({
  data: Array,
});
</script>

<template>
  <div class="flex">
    <TextCheckContainerEditor :data="data" />
  </div>
</template>
