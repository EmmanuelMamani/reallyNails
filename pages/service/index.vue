<template>
  <div class="p-3">
    <h3 class="text-center text-xl">Servicios</h3>

    <div class="flex justify-end w-11/12 mx-auto space-x-3">
      <ServiceWeekPicker class="w-3/5" @semana-seleccionada="filtrarPorSemana" />
      <ServiceCreate @service-created="agregarServicio" />
    </div>

    <div class="w-11/12 mx-auto mt-4 overflow-x-auto">
      <el-table
          :data="services"
          style="min-width: 700px"
          class="whitespace-nowrap"
      >
        <el-table-column prop="employee" label="Personal" />
        <el-table-column prop="service" label="Servicio" />
        <el-table-column prop="price" label="Costo" />
        <el-table-column prop="comment" label="Comentario" />
        <el-table-column label="Fecha">
          <template #default="scope">
            {{ formatFecha(scope.row.created_at) }}
          </template>
        </el-table-column>

        <el-table-column label="Acciones" width="120">
          <template #default="scope">
            <ServiceDelete :id="scope.row.id" @deleted="removerServicio" />
          </template>
        </el-table-column>

        <template #append v-if="services.length > 0">
          <tr>
            <td colspan="2"><strong>Total de la semana:</strong></td>
            <td><strong>{{ totalSemana.toFixed(2) }} Bs</strong></td>
            <td colspan="3"></td>
          </tr>
        </template>
      </el-table>
    </div>

  </div>
</template>

<script setup>
import dayjs from 'dayjs'
import isoWeek from 'dayjs/plugin/isoWeek'
import utc from 'dayjs/plugin/utc'

dayjs.extend(isoWeek)
dayjs.extend(utc)

const services = ref([])
const supabase = useSupabaseClient()

async function filtrarPorSemana({ from, to }) {
  const { data, error } = await supabase
      .from('services')
      .select('*')
      .gte('created_at', from)
      .lte('created_at', to)
      .order('created_at', { ascending: false })

  if (error) {
    console.error('Error al filtrar:', error.message)
  } else {
    services.value = data
  }
}

function agregarServicio() {
  const monday = dayjs().utc().startOf('isoWeek').startOf('day').toISOString()
  const sunday = dayjs().utc().endOf('isoWeek').endOf('day').toISOString()
  filtrarPorSemana({ from: monday, to: sunday })
}

function removerServicio(id) {
  services.value = services.value.filter(s => s.id !== id)
}

function formatFecha(fecha) {
  const date = new Date(fecha)
  return date.toLocaleDateString('es-BO', {
    weekday: 'long',
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  })
}

const totalSemana = computed(() =>
    services.value.reduce((sum, s) => sum + (s.price || 0), 0)
)

onMounted(() => {
  agregarServicio()
})

</script>
