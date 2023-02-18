<script setup lang="ts">
import type { Ref } from "vue";
import { onMounted, reactive } from "vue";
import { Html5QrcodeScanner } from "html5-qrcode";
import axios from "axios";

let result = reactive({
  wedding_code: '',
  guest_id: 0,
  user_id: null
});

let guest = reactive({
  name: null,
  category: {
    name: null
  },
});

const onScanSuccess = (decodedText: string, decodedResult: any) => {
  result = decodedResult;
  getGuestInfo()
}

const onScanFailure = (error: any) => {
  // handle scan failure, usually better to ignore and keep scanning.
  // for example:
  // console.warn(`Code scan error = ${error}`);
}

const getGuestInfo = async () => {
  if (!result) return;

  try {
    const response = await axios.post(
      'https://api.hummingbird.id/api/wedding-guest/check', {
        wedding_code: result.wedding_code,
        guest_id: result.guest_id
      }
    );
    guest.name = response.data.name
    if (response.data.category) {
      guest.category.name = response.data.category.name
    }
  } catch (err: any) {
    console.log(err);
  }
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
    <div v-if="guest.name" id="data">
      <div align="center">
        <img src="@/assets/icons/check-mark.png" width="80" alt="">
        <h3 style="padding: 1rem 0">
          Successfully scanned guest!
        </h3>
      </div>
      <div style="background-color: white; color: black; padding: 1rem; border-radius: 10px;">
        <h3 style="font-weight: 600;">
          Guest Information
        </h3>
        <p>
          <strong>Name:</strong> {{ guest.name }}
        </p>
        <p v-if="guest.category.name">
          <strong>Category:</strong> {{ guest.category.name }}
        </p>
      </div>
    </div>
    <div v-if="!guest.name" id="reader"></div>
  </div>
</template>

<style>
#reader, #data {
  border-radius: 10px;
  border: 1px solid white;
  padding: 2rem;
}

#reader {
  width: 100%;
  background-color: white;
}

#data {
  margin-bottom: 32px;
  background-color: rgba(0, 0, 0, .5);
}
</style>
