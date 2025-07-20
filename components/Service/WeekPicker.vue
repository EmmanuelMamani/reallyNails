<template>
  <div>
    <div class="flex items-center gap-2">
      <el-date-picker
          v-model="selectedDate"
          type="date"
          format="[Semana] WW, YYYY"
          value-format="YYYY-MM-DD"
          placeholder="Selecciona una fecha"
          @change="updateWeekInfo"
          size="small"
      />
      <el-button type="primary" @click="emitWeek" >Filtrar</el-button>
    </div>
    <div v-if="currentWeekInfo" class="ml-2 text-xs text-gray-500">
      {{ currentWeekInfo }}
    </div>
  </div>
</template>

<script setup>
import dayjs from 'dayjs'
import isoWeek from 'dayjs/plugin/isoWeek'
import utc from 'dayjs/plugin/utc'

dayjs.extend(isoWeek)
dayjs.extend(utc)

const emit = defineEmits(['semana-seleccionada'])

const selectedDate = ref(null)
const currentWeekInfo = ref('')

function updateWeekInfo(date) {
  if (!date) {
    currentWeekInfo.value = ''
    return
  }

  const d = dayjs(date)
  const weekNumber = d.isoWeek()
  const year = d.year()
  currentWeekInfo.value = `Semana ${weekNumber}, ${year} (Lun ${d.startOf('isoWeek').format('DD/MM')} - Dom ${d.endOf('isoWeek').format('DD/MM')})`
}

function getWeekRange(date) {
  const d = dayjs(date)
  return {
    from: d.startOf('isoWeek').toISOString(),
    to: d.endOf('isoWeek').toISOString()
  }
}

function emitWeek() {
  if (!selectedDate.value) return

  const { from, to } = getWeekRange(selectedDate.value)
  const d = dayjs(selectedDate.value)
  const weekNumber = d.isoWeek()
  const year = d.year()

  emit('semana-seleccionada', {
    from,
    to,
    weekNumber,
    year
  })
}
</script>