<template>
    <div>
         <el-button @click="open=true" type="primary" >Crear tarjeta</el-button>
    </div>
    <el-dialog v-model="open" title="Crear Tarjeta" width="500">
    <el-form :model="form" label-width="auto">
      <el-form-item label="Para:">
        <el-input v-model="form.to" autocomplete="off" />
      </el-form-item>
      <el-form-item label="Mensaje:">
        <el-input v-model="form.description" autocomplete="off" :rows="2" type="textarea" />
      </el-form-item>
    </el-form>
    <template #footer>
      <div class="dialog-footer">
        <el-button @click="open=false">Cancelar</el-button>
        <el-button type="primary" @click="create">Crear</el-button>
      </div>
    </template>
  </el-dialog>
</template>
<script setup>
    const open = ref(false)
    const supabase = useSupabaseClient()
    const emit = defineEmits(['card-created'])
    const form = ref({to:'',from:'',description:''})
    async function create() {
        const { data, error } = await supabase
        .from('gif_cards')
        .insert([
            { to: form.value.to,description:form.value.description},
        ])
        .select()
        form.value={to:'',description:''}
        if(data){
            ElNotification({
                title: 'Éxito',
                message: 'Tarjeta creada correctamente 🎉',
                type: 'success',
            })
            emit('card-created', data[0])
            open.value=false
        }
    }
</script>