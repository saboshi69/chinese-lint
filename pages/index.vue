<script setup>
// import { google } from "googleapis";
const { data, error, pending } = await useAsyncData("dictionary", async () => {
  const wrong = await $fetch(
    `https://sheets.googleapis.com/v4/spreadsheets/${process.env.SHEET_ID}/values/Dict1!A%3AA?key=${process.env.KEY}`
  );
  const right = await $fetch(
    `https://sheets.googleapis.com/v4/spreadsheets/${process.env.SHEET_ID}/values/Dict1!B%3AB?key=${process.env.KEY}`
  );
  return [wrong, right];
});
</script>

<template>
  <div class="container mx-auto p-8">
    <!-- <p>{{ data }}</p> -->
    <p v-if="pending">initializing...</p>
    <p v-if="error">something wrong sor</p>
    <div v-if="data">
      <p>successfully initialize!</p>
      <TextCheckContainer :data="data" />
    </div>
  </div>
</template>
