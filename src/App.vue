<template>
  <div class="page">
    <h1>Vue Nested iframe</h1>

    <!-- outer iframe -->
    <iframe ref="outerFrame" class="outer-frame" @load="onOuterLoad"></iframe>
  </div>
</template>

<script>
export default {
  data() {
    return {
      base64Html: "",
    };
  },

  mounted() {
    this.loadHtml();
  },

  methods: {
    async loadHtml() {
      try {
        const res = await fetch("/embed.html");
        const text = await res.text();

        // base64 변환
        this.base64Html = btoa(unescape(encodeURIComponent(text)));

        this.renderOuterIframe();
      } catch (e) {
        console.error(e);
      }
    },

    renderOuterIframe() {
      const outerDoc =
        this.$refs.outerFrame.contentDocument ||
        this.$refs.outerFrame.contentWindow.document;

      outerDoc.open();
      outerDoc.write(`
        <!DOCTYPE html>
        <html>
        <body style="margin:0">
          <h3>Outer iframe</h3>

          <iframe 
            id="innerFrame"
            style="width:100%;height:300px;border:1px solid #aaa"
          ></iframe>

          <script>
            const iframe = document.getElementById("innerFrame");
            iframe.src = "data:text/html;base64,${this.base64Html}";
          <\/script>

        </body>
        </html>
      `);
      outerDoc.close();
    },

    onOuterLoad() {
      // 필요하면 여기서 후처리 가능
    },
  },
};
</script>

<style>
.page {
  padding: 20px;
}

.outer-frame {
  width: 100%;
  height: 400px;
  border: 2px solid #444;
}
</style>
