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
      <a
        class="hover:underline text-green-500 inline-flex items-center mb-1"
        target="_blank"
        href="https://docs.google.com/spreadsheets/d/1qYJxQe3RcimD_bTFdSCdYDcbGzIMsVK0IkZ68p3jZus/edit?usp=sharing"
      >
        Database connected<span class="pl-1"
          ><i class="ri-checkbox-circle-fill text-green-500"></i
        ></span>
      </a>
      <TextCheckContainer :data="data" />
    </div>
  </div>
</template>
