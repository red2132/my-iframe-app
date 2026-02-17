<template>
  <div class="container">
    <h2>Vue Base64 Iframe Example</h2>

    <iframe v-if="iframeSrc" :src="iframeSrc" class="frame" />
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const iframeSrc = ref("");

onMounted(async () => {
  // public 폴더의 html 파일 가져오기
  const response = await fetch("/embed.html");
  const htmlText = await response.text();

  // base64 인코딩
  const encoded = btoa(unescape(encodeURIComponent(htmlText)));

  iframeSrc.value = `data:text/html;base64,${encoded}`;
});
</script>

<style>
.container {
  text-align: center;
  padding: 40px;
}

.frame {
  width: 100%;
  max-width: 800px;
  height: 500px;
  border: none;
  margin-top: 20px;
  border-radius: 12px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.15);
}
</style>
