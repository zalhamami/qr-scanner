<script setup lang="ts">
import { onMounted, ref } from "vue";
import { Html5QrcodeScanner } from "html5-qrcode"

let text = ref('');
let result = ref(null);

const onScanSuccess = (decodedText: string, decodedResult: any) => {
  text.value = `Code matched = ${decodedText}`;
  result.value = decodedResult;
}

function onScanFailure(error: any) {
  // handle scan failure, usually better to ignore and keep scanning.
  // for example:
  console.warn(`Code scan error = ${error}`);
}

onMounted(() => {
  const html5QrcodeScanner = new Html5QrcodeScanner(
  "reader",
  { fps: 10, qrbox: {width: 200, height: 200} },
  /* verbose= */ false);
  html5QrcodeScanner.render(onScanSuccess, onScanFailure);
});
</script>

<template>
  <div class="scanner-view">
    <div v-if="text" id="data">
      {{ text }}<br>
      {{ result }}
    </div>
    <div id="reader"></div>
  </div>
</template>

<style>
#reader {
  width: 100%;
}

#data {
  margin-bottom: 32px;
}
</style>
