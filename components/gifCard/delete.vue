<template>
    <div>
        <el-popconfirm
        title="Estas seguro de que lo quieres eliminar?"
        placement="top"
        confirm-button-text="Sí"
        cancel-button-text="No"
        @confirm="handleDelete">
        <template #reference>
          <el-button type="danger">Eliminar</el-button>
        </template>
      </el-popconfirm>
    </div>
</template>
<script setup>
    const supabase = useSupabaseClient()
    const emit = defineEmits(['deleted'])

    const props = defineProps({
                        id: { type: [Number, String], required: true} 
                })

    async function handleDelete() {
        const { error } = await supabase
            .from('gif_cards')
            .delete()
            .eq('id', props.id)

        if (!error) {
            ElNotification({
                title: 'Exito',
                message: 'Tarjeta eliminada exitosamente',
                type: 'success',
            })
            emit('deleted', props.id)

        } else {
            ElNotification({
                title: 'Ups!',
                message: 'Algo salio mal al intentar borrar',
                type: 'error',
            })
        }
    }
</script>