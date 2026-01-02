<script setup lang="ts">
  import liff from '@line/liff';
  import { onMounted } from 'vue';

  const profile = ref<any>(null);
  
  // Import runtime config for env variables
  const runtimeConfig = useRuntimeConfig();
  const liffId = runtimeConfig.public.LIFF_ID;
  
  // Init LIFF when DOM is mounted
  // https://vuejs.org/api/composition-api-lifecycle.html#onmounted
  onMounted(async () => {
    if(!liffId) {
      console.error('Please set LIFF_ID in .env file')
      return
    };
  
    await liff.init({ liffId: liffId });
    console.log('LIFF init success');
    console.log('LIFF SDK version', liff.getVersion());


    profile.value = await liff.getProfile()
  })
  </script>


<template>
  <div class="home">
    <h1 class="home__title">
      Welcome to Twjoin F2E
    </h1>

    <p>
      Hi {{ profile }}<br/>
    </p>
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
