<template>
  <div>
    <el-button @click="open = true" type="primary">Crear servicio</el-button>
  </div>

  <el-dialog v-model="open" title="Registrar nuevo servicio" class="!w-11/12 lg:!w-1/2">
    <el-form
        ref="formRef"
        :model="form"
        :rules="rules"
        label-width="100px"
        label-position="left"
    >
      <el-form-item label="Personal" prop="employee">
        <el-select v-model="form.employee" placeholder="Selecciona personal">
          <el-option
              v-for="item in empleados"
              :key="item"
              :label="item"
              :value="item"
          />
        </el-select>
      </el-form-item>

      <el-form-item label="Servicio" prop="service">
        <el-autocomplete
            v-model="form.service"
            :fetch-suggestions="querySearch"
            placeholder="Ej. gel, tradicional, etc."
        />
      </el-form-item>

      <el-form-item label="Precio (Bs)" prop="price">
        <el-input v-model.number="form.price" type="number" step="0.01" />
      </el-form-item>

      <el-form-item label="Comentarios (opcional)">
        <el-input
            v-model="form.comment"
            type="textarea"
            :rows="2"
            placeholder="Ej. cliente llegó tarde, uñas frágiles..."
        />
      </el-form-item>
    </el-form>

    <template #footer>
      <div class="dialog-footer">
        <el-button @click="open = false">Cancelar</el-button>
        <el-button type="primary" @click="create">Guardar</el-button>
      </div>
    </template>
  </el-dialog>
</template>

<script setup>
import { ref } from 'vue'
import { ElNotification } from 'element-plus'

const open = ref(false)
const supabase = useSupabaseClient()
const emit = defineEmits(['service-created'])

const empleados = ['Raquel', 'Sthefany']
const servicios = ['gel', 'tradicional', 'esculpidas', 'acrílicas', 'softgel', 'poligel']

const form = ref({
  employee: '',
  service: '',
  price: null,
  comment: '',
})

const rules = {
  employee: [{ required: true, message: 'Selecciona personal', trigger: 'blur' }],
  service: [{ required: true, message: 'El servicio es obligatorio', trigger: 'blur' }],
  price: [{ required: true, message: 'El precio es obligatorio', trigger: 'blur' }],
}

const formRef = ref()

function querySearch(queryString, cb) {
  const results = servicios.filter(item =>
      item.toLowerCase().includes(queryString.toLowerCase())
  )
  cb(results.map(s => ({ value: s })))
}

async function create() {
  formRef.value.validate(async valid => {
    if (!valid) return

    const { data, error } = await supabase
        .from('services')
        .insert([
          {
            employee: form.value.employee,
            service: form.value.service,
            price: form.value.price,
            created_at: new Date().toLocaleString('sv-SE', { timeZone: 'America/La_Paz' }),
            comment: form.value.comment || null,
          },
        ])
        .select()

    if (error) {
      ElNotification({
        title: 'Error',
        message: error.message,
        type: 'error',
      })
      return
    }

    form.value = {
      employee: '',
      service: '',
      price: null,
      comments: '',
    }

    ElNotification({
      title: 'Éxito',
      message: 'Servicio registrado correctamente 💅',
      type: 'success',
    })
    emit('service-created', data[0])
    open.value = false
  })
}
</script>
