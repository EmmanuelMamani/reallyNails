<template>
  <div class="p-4">
    <h1 class="text-2xl font-bold mb-4 text-center">Tarjetas MOM</h1>
    <div class="flex justify-end w-5/6 mx-auto">
      <momCardCreate @card-created="insertarTarjeta"></momCardCreate>
    </div>
    <div v-if="loading" class="text-gray-500 text-center">Cargando tarjetas...</div>
    <div v-else-if="momCards.length === 0" class="text-gray-500">No hay tarjetas aún.</div>

    <div class="w-5/6 mx-auto">
      <el-table v-if="!loading" :data="momCards" style="width: 100%">
        <el-table-column prop="to" label="Para" />
        <el-table-column prop="from" label="De" />
        <el-table-column prop="description" label="Mensaje" />
        <el-table-column label="Eliminar" width="100">
          <template #default="scope">
            <momCardDelete :id="scope.row.id" @deleted="handleDeleted"></momCardDelete>
          </template>
        </el-table-column>
        <el-table-column label="Editar" width="100">
          <template #default="scope">
            <momCardUpdate :momCard="scope.row" @card-update="handleUpdated" />
          </template>
        </el-table-column>
         <el-table-column label="QR" width="100">
          <template #default="scope">
            <momCardQr :id="scope.row.id"></momCardQr>
          </template>
        </el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script setup>
const supabase = useSupabaseClient()
const momCards = ref([])
const loading = ref(true)

const fetchMomCards = async () => {
  const { data, error } = await supabase
    .from('mom_cards')
    .select('*')
    .order('created_at', { ascending: false })

  if (error) {
    console.error('Error al obtener tarjetas:', error.message)
  } else {
    momCards.value = data
  }

  loading.value = false
}
function insertarTarjeta(card) {
  momCards.value.unshift(card)
}
function handleDeleted(id) {
  momCards.value = momCards.value.filter(card => card.id !== id)
}
function handleUpdated(updatedCard) {
  const index = momCards.value.findIndex(card => card.id === updatedCard.id)
  if (index !== -1) {
    momCards.value[index] = { ...momCards.value[index], ...updatedCard }
  }
}


onMounted(fetchMomCards)
</script>
