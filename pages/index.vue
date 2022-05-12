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
useHead({
  title: "中文錯字偵測工具",
  titleTemplate: "中文錯字偵測工具",
  viewport: "width=device-width, initial-scale=1, maximum-scale=1",
  charset: "utf-8",
  meta: [
    { name: "viewport", content: "width=device-width, initial-scale=1" },
    {
      hid: "開源中文錯字偵測工具",
      name: "開源中文錯字偵測工具",
      content: "解放雙眼，精準校對，一鍵偵測錯別字。",
    },
  ],
  link: [
    { rel: "icon", type: "image/x-icon", href: "/favicon/favicon.ico" },
    { rel: "icon", type: "image/png", href: "/favicon/favicon-32x32.png" },
    { rel: "icon", type: "image/png", href: "/favicon/favicon-16x16.png" },
    {
      rel: "manifest",
      type: "image/x-icon",
      href: "/favicon/site.webmanifest",
    },
    {
      rel: "apple-touch-icon",
      type: "image/x-icon",
      sizes: "180x180",
      href: "/favicon/apple-touch-icon.png",
    },
  ],
});
</script>

<template>
  <div class="container mx-auto p-8">
    <h3 class="text-[32px]">中文錯字偵測工具</h3>
    <p v-if="pending">準備啟動中...</p>
    <p v-if="error">連結資料庫時發生錯誤</p>
    <div v-if="data">
      <div class="flex w-full mb-1">
        <a
          class="hover:underline text-green-500 inline-flex items-center mb-1"
          target="_blank"
          href="https://docs.google.com/spreadsheets/d/1qYJxQe3RcimD_bTFdSCdYDcbGzIMsVK0IkZ68p3jZus/edit?usp=sharing"
        >
          成功連結資料庫<span class="pl-1"
            ><i class="ri-checkbox-circle-fill text-green-500"></i
          ></span>
        </a>
        <div class="px-6 flex items-center mb-1">
          <a
            class="hover:underline inline-block items-center"
            target="_blank"
            href="https://github.com/saboshi69/chinese-lint"
          >
            前往Github
          </a>
        </div>
      </div>
      <TextCheckContainer :data="data" />
    </div>
  </div>
</template>
