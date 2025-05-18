<template>
    <div>
         <el-button @click="open=true" type="primary" >QR</el-button>
    </div>
    <el-dialog v-model="open" title="QR" width="500" :append-to-body="true">
        <canvas ref="canvasRef" class="w-3/4 mx-auto"></canvas>
  </el-dialog>
</template>
<script setup>

import QRCode from 'qrcode'

    const open = ref(false)
    const canvasRef = ref(null)

    const props = defineProps({
    id: { type: [Number, String], required: true }
    })

    watch(open, (newVal) => {
    if (newVal) {
        setTimeout(() => {
        if (canvasRef.value) {
            QRCode.toCanvas(canvasRef.value, `https://algo/${props.id}`, {
            width: 300,
            errorCorrectionLevel: 'H',
            color: {
                dark: '#000000',
                light: '#ffffff'
            }
            }, (error) => {
                if (error) console.error(error)
            })
        }
        }, 100)
    }
    })
</script>
