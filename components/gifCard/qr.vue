<template>
  <div>
    <el-button @click="open = true" type="primary">QR</el-button>

    <el-dialog v-model="open" title="QR" width="500" :append-to-body="true">
      <div ref="qrContainer" class="w-full flex justify-center items-center"></div>
    </el-dialog>
  </div>
</template>

<script setup>
import { ref, watch, nextTick } from 'vue'
import QRCodeStyling from 'qr-code-styling'

const open = ref(false)
const qrContainer = ref(null)

const props = defineProps({
  id: {
    type: [String, Number],
    required: true
  }
})

let qrCode = new QRCodeStyling({
  width: 300,
  height: 300,
  data: `https://really-nails.vercel.app/gifCard/${props.id}`,
  image: "/img/logoRnBlack.png",
  dotsOptions: {
    color: "#000000",
    type: "rounded"
  },
  backgroundOptions: {
    color: "#ffffff"
  },
  imageOptions: {
    crossOrigin: "anonymous",
    margin: 5,
    imageSize: 0.25
  }
})

watch(open, async (newVal) => {
  if (newVal) {
    await nextTick() // espera a que el DOM esté completamente listo
    if (qrContainer.value) {
      qrContainer.value.innerHTML = ''
      qrCode.append(qrContainer.value)
    }
  }
})
</script>
