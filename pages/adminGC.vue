<template>
  <div class="p-4">
    <h1 class="text-2xl font-bold mb-4 text-center">Tarjetas</h1>
    <div class="flex justify-end w-5/6 mx-auto">
      <gifCardCreate @card-created="insertarTarjeta"></gifCardCreate>
    </div>
    <div v-if="loading" class="text-gray-500 text-center">Cargando tarjetas...</div>
    <div v-else-if="gifCards.length === 0" class="text-gray-500">No hay tarjetas aún.</div>

    <div class="w-5/6 mx-auto">
      <el-table v-if="!loading" :data="gifCards" style="width: 100%">
        <el-table-column prop="to" label="Para" />
        <el-table-column prop="description" label="Mensaje" />
        <el-table-column label="Eliminar" width="100">
          <template #default="scope">
            <gifCardDelete :id="scope.row.id" @deleted="handleDeleted"></gifCardDelete>
          </template>
        </el-table-column>
        <el-table-column label="Editar" width="100">
          <template #default="scope">
            <gifCardUpdate :gifCard="scope.row" @card-update="handleUpdated" />
          </template>
        </el-table-column>
         <el-table-column label="QR" width="100">
          <template #default="scope">
            <gifCardQr :id="scope.row.id"></gifCardQr>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script setup>
const supabase = useSupabaseClient()
const gifCards = ref([])
const loading = ref(true)

const fetchGifCards = async () => {
  const { data, error } = await supabase
    .from('gif_cards')
    .select('*')
    .order('created_at', { ascending: false })

  if (error) {
    console.error('Error al obtener tarjetas:', error.message)
  } else {
    gifCards.value = data
  }

  loading.value = false
}
function insertarTarjeta(card) {
  gifCards.value.unshift(card)
}
function handleDeleted(id) {
  gifCards.value = gifCards.value.filter(card => card.id !== id)
}
function handleUpdated(updatedCard) {
  const index = gifCards.value.findIndex(card => card.id === updatedCard.id)
  if (index !== -1) {
    gifCards.value[index] = { ...gifCards.value[index], ...updatedCard }
  }
}


onMounted(fetchGifCards)
</script>
