<template>
  <div>
    <el-button @click="open = true" type="primary">Editar</el-button>
  </div>

  <el-dialog v-model="open" title="Editar Tarjeta" width="500" :append-to-body="true">
    <el-form :model="form" label-width="auto">
      <el-form-item label="Para:">
        <el-input v-model="form.to" autocomplete="off" />
      </el-form-item>
      <el-form-item label="Mensaje:">
        <el-input v-model="form.description" type="textarea" :rows="2" autocomplete="off" />
      </el-form-item>
    </el-form>

    <template #footer>
      <div class="dialog-footer">
        <el-button @click="open = false">Cancelar</el-button>
        <el-button type="primary" @click="update">Actualizar</el-button>
      </div>
    </template>
  </el-dialog>
</template>

<script setup>

const open = ref(false)
const supabase = useSupabaseClient()

const props = defineProps({
  gifCard: { type: Object, required: true }
})

const emit = defineEmits(['card-update'])

const form = ref({
  to: props.gifCard.to,
  description: props.gifCard.description
})

async function update() {
  const { data, error } = await supabase
    .from('gif_cards')
    .update({
      to: form.value.to,
      description: form.value.description
    })
    .eq('id', props.gifCard.id)
    .select()

  if (error) {
    ElNotification({
      title: 'Error',
      message: 'Hubo un problema al actualizar la tarjeta',
      type: 'error'
    })
    return
  }

  if (data) {
    ElNotification({
      title: 'Éxito',
      message: 'Tarjeta editada correctamente 🎉',
      type: 'success'
    })
    emit('card-update', data[0])
    open.value = false
  }
}
</script>
