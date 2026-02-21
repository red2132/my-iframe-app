<template>
  <div class="container">
    <h2>Base64 → Blob → Iframe 렌더링</h2>

    <iframe
      v-if="iframeSrc"
      :src="iframeSrc"
      class="frame"
      sandbox="allow-scripts"
    />
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from "vue";

const iframeSrc = ref(null);
let objectUrl = null;

onMounted(async () => {
  // 1️⃣ HTML 파일 가져오기
  const response = await fetch("/embed.html");
  const htmlText = await response.text();

  // 2️⃣ base64 인코딩
  const base64 = btoa(unescape(encodeURIComponent(htmlText)));

  // 3️⃣ base64 → 바이너리 변환
  const byteCharacters = atob(base64);
  const byteNumbers = new Array(byteCharacters.length);

  for (let i = 0; i < byteCharacters.length; i++) {
    byteNumbers[i] = byteCharacters.charCodeAt(i);
  }

  const byteArray = new Uint8Array(byteNumbers);

  // 4️⃣ Blob 생성
  const blob = new Blob([byteArray], { type: "text/html" });

  // 5️⃣ Blob URL 생성
  objectUrl = URL.createObjectURL(blob);

  // 6️⃣ iframe에 적용
  iframeSrc.value = objectUrl;
});

// 메모리 정리 (중요 ⭐)
onBeforeUnmount(() => {
  if (objectUrl) {
    URL.revokeObjectURL(objectUrl);
  }
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
