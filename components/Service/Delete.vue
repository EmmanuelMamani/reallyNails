<template>
  <div>
    <el-popconfirm
        title="¿Estás seguro de que deseas eliminar este servicio?"
        placement="top"
        confirm-button-text="Sí"
        cancel-button-text="No"
        @confirm="handleDelete"
    >
      <template #reference>
        <el-button type="danger" size="small">Eliminar</el-button>
      </template>
    </el-popconfirm>
  </div>
</template>

<script setup>
const supabase = useSupabaseClient()
const emit = defineEmits(['deleted'])

const props = defineProps({
  id: { type: [Number, String], required: true },
})

async function handleDelete() {
  const { error } = await supabase
      .from('services') // ahora apunta a la tabla correcta
      .delete()
      .eq('id', props.id)

  if (!error) {
    ElNotification({
      title: 'Éxito',
      message: 'Servicio eliminado correctamente',
      type: 'success',
    })
    emit('deleted', props.id)
  } else {
    ElNotification({
      title: 'Ups!',
      message: 'Ocurrió un error al eliminar el servicio',
      type: 'error',
    })
  }
}
</script>
