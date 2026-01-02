<script setup lang="ts">
import liff from "@line/liff";
import { onMounted, ref } from "vue";

const name = ref<string>("");
const pictureUrl = ref<string>("");
const error = ref<any>(null);

// Import runtime config for env variables
const runtimeConfig = useRuntimeConfig();
const liffId = runtimeConfig.public.LIFF_ID;

// Init LIFF when DOM is mounted
// https://vuejs.org/api/composition-api-lifecycle.html#onmounted
onMounted(async () => {
  console.log('liffId:',liffId); // TODO: Will delete
  
  if (!liffId) {
    console.error("Please set LIFF_ID in .env file");
    return;
  }

  try {
    await liff.init({ liffId: liffId });
    console.log("LIFF init success");
    console.log("LIFF SDK version", liff.getVersion());

    // 檢查使用者是否已登入
    if (!liff.isLoggedIn()) {
      // 如果未登入，導向登入頁面
      liff.login();
      return;
    }

    // 取得使用者資訊
    const profile = await liff.getProfile();
    name.value = profile.displayName;
    pictureUrl.value = profile.pictureUrl || "";
    console.log("User profile:", profile);
  } catch (err) {
    error.value = err;
    console.error("LIFF error:", err);
  }
});

const sendToChatroom = async () => {
  liff
  .sendMessages([
    {
      type: "text",
      text: "測試貓貓喝咖啡",
    },
  ])
  .then(() => {
    console.log("message sent");
  })
  .catch((err) => {
    console.log("error", err);
  });
}
</script>

<template>
  <div class="home">
    <h1 class="home__title">Welcome to Twjoin F2E</h1>

    <p>Hi {{ name }}<br /></p>
    <img :src="pictureUrl" alt="test" height="100" width="100" />
    
    <button @click="sendToChatroom">Send to Chatroom</button>
  </div>
</template>

<style>
html,
body {
  font-family: -apple-system, BlinkMacSystemFont, Segoe UI, Roboto, Oxygen,
    Ubuntu, Cantarell, Fira Sans, Droid Sans, Helvetica Neue, sans-serif;
  margin: 0;
  padding: 0;

  margin-left: 30px;
}
</style>
