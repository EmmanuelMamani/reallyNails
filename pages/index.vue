<template>
  <div>
    <h1 class="text-2xl font-bold mb-4">Tarjetas MOM</h1>
    <ul>
      <li v-for="card in momCards" :key="card.id" class="mb-2">
        {{ card.form }} - {{ card.to }}
      </li>
    </ul>
  </div>
</template>
<script setup >

const supabase = useSupabaseClient()
const momCards = ref([])

const { data, error } = await useAsyncData('mom_cards', async () => {
  const { data, error } = await supabase
    .from('mom_cards')
    .select('*')
    .order('created_at', { ascending: false })

  if (error) {
    console.error('Error al obtener mom_cards:', error.message)
    return []
  }

  return data
})

momCards.value = data.value
</script>


