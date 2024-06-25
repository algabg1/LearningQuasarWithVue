<template>
  <q-page padding>
    <div class="row q-col-gutter-md">
      <div class="col-12">
        <h2 class="text-h4 q-mb-md">{{ isAdmin ? 'Todas as Plantas' : 'Plantas' }}</h2>
      </div>
      <div class="col-12 col-md-6">
        <q-input v-model="searchTerm" label="Pesquisar plantas" dense>
          <template v-slot:append>
            <q-icon name="search" />
          </template>
        </q-input>
      </div>
      <div class="col-12 col-md-6">
        <q-select
          v-model="selectedType"
          :options="plantsTypes"
          label="Filtrar por tipo"
          dense
          emit-value
          map-options
        >
          <template v-slot:prepend>
            <q-icon name="filter_list" />
          </template>
        </q-select>
      </div>
      <div class="col-12">
        <q-btn color="primary" icon="add" label="Nova planta" @click="openNewProjectDialog" />
      </div>
      <div v-if="loading" class="col-12 flex flex-center">
        <q-spinner color="primary" size="3em" />
      </div>
      <template v-else-if="filteredPlants.length">
        <div v-for="plant in filteredPlants" :key="plant.id" class="col-12 col-sm-6 col-md-4">
          <q-card class="project-card">
            <q-card-section>
              <div class="text-h6">{{ plant.nome }}</div>
              <q-chip :color="getProjectTypeColor(plant.categoria)" text-color="white">
                {{ plant.categoria }}
              </q-chip>
            </q-card-section>
            <q-card-section>
              <p>{{ plant.nomecientifico }}</p>
            </q-card-section>
            <q-card-actions align="right">
              <q-btn flat color="primary" label="Editar" @click="editProject(plant)" />
              <q-btn flat color="negative" label="Excluir" @click="confirmDeleteProject(plant)" />
            </q-card-actions>
          </q-card>
        </div>
      </template>
      <div v-else class="col-12 text-center">
        <p>Nenhum planta encontrada.</p>
      </div>
    </div>

    <!-- New Plant Dialog -->
    <q-dialog v-model="newPlantDialog" persistent>
      <q-card style="min-width: 350px">
        <q-card-section>
          <div class="text-h6">Nova planta</div>
        </q-card-section>
        <q-card-section class="q-pt-none">
          <q-input v-model="newPlant.nome" label="Nome da Planta" dense />
          <q-input v-model="newPlant.nome_cientifico" label="Nome Científico" dense />
          <q-input v-model="newPlant.descricao" label="Descrição" type="textarea" dense />
          <q-input v-model="newPlant.origem" label="Origem" dense />
          <q-input v-model="newPlant.cuidados" label="Cuidados" type="textarea" dense />
          <q-input v-model="newPlant.dataregistro" label="Data de Registro" type="date" dense />
          <q-select
            v-model="newPlant.categoria"
            :options="projectTypesForNewProject"
            label="Categoria"
            dense
          />
        </q-card-section>
        <q-card-actions align="right" class="text-primary">
          <q-btn flat label="Cancelar" v-close-popup @click="resetNewProject" />
          <q-btn flat label="Salvar" @click="saveNewProject" />
        </q-card-actions>
      </q-card>
    </q-dialog>

    <!-- Confirm Delete Dialog -->
    <q-dialog v-model="confirmDelete">
      <q-card>
        <q-card-section class="row items-center">
          <q-avatar icon="delete" color="negative" text-color="white" />
          <span class="q-ml-sm">Tem certeza que deseja excluir este projeto?</span>
        </q-card-section>
        <q-card-actions align="right">
          <q-btn flat label="Cancelar" color="primary" v-close-popup />
          <q-btn flat label="Excluir" color="negative" @click="deleteProject" v-close-popup />
        </q-card-actions>
      </q-card>
    </q-dialog>
  </q-page>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import { useQuasar } from 'quasar'
import { api } from 'boot/axios'

const $q = useQuasar()
const plants = ref([])
const loading = ref(true)
const newPlantDialog = ref(false)
const confirmDelete = ref(false)
const projectToDelete = ref(null)
const searchTerm = ref('')
const selectedType = ref(null)

const user = ref(JSON.parse(localStorage.getItem('userData')))
const isAdmin = computed(() => user.value.user_role === 'ADMIN')

const newPlant = ref({
  nome: '',
  nomecientifico: '',
  descricao: '',
  origem: '',
  cuidados: '',
  dataregistro: null, // ou uma data padrão, se necessário
  categoria: ''
})

const plantsTypes = [
  { label: 'Todos', value: null },
  { label: 'Briofita', value: 'BRIOFITA' },
  { label: 'Pteridófita', value: 'PTERIDOFITA' },
  { label: 'Gimnosperma', value: 'GIMNOSPERMA' },
  { label: 'Angiosperma', value: 'ANGIOSPERMA' },
  { label: 'Herbácea', value: 'HERBACEA' },
  { label: 'Arbusto', value: 'ARBUSTO' },
  { label: 'Árvore', value: 'ARVORE' },
  { label: 'Trepadeira', value: 'TREPADEIRA' },
  { label: 'Suculenta', value: 'SUCULENTA' },
  { label: 'Cacto', value: 'CACTO' },
  { label: 'Orquídea', value: 'ORQUIDEA' },
  { label: 'Bromélia', value: 'BROMELIA' },
  { label: 'Aerófita', value: 'AEROFITA' },
  { label: 'Hidrófita', value: 'HIDROFITA' },
  { label: 'Xerófita', value: 'XEROFITA' },
  { label: 'Litófita', value: 'LITOFITA' },
  { label: 'Epífita', value: 'EPIFITA' },
  { label: 'Campestre', value: 'CAMPESTRE' },
  { label: 'Ripícola', value: 'RIPICOLA' },
  { label: 'Parasita', value: 'PARASITA' },
  { label: 'Carnívora', value: 'CARNIVORA' },
  { label: 'Aquática', value: 'AQUATICA' },
  { label: 'Lagunar', value: 'LAGUNAR' },
  { label: 'Florestal', value: 'FLORESTAL' },
  { label: 'Camponesa', value: 'CAMPONESA' }
]

const projectTypesForNewProject = computed(() =>
  plantsTypes.filter(type => type.value !== null)
)

const filteredPlants = computed(() => {
  return plants.value.filter(plant => {
    const matchesSearch = plant.nome.toLowerCase().includes(searchTerm.value.toLowerCase()) ||
                          plant.nome_cientifico.toLowerCase().includes(searchTerm.value.toLowerCase())
    const matchesType = !selectedType.value || plant.categoria === selectedType.value
    return matchesSearch && matchesType
  })
})

onMounted(async () => {
  await fetchProjects()
})

async function fetchProjects () {
  try {
    const token = localStorage.getItem('userToken')
    const endpoint = isAdmin.value
      ? 'http://localhost:8080/planta/plantas'
      : `http://localhost:8080/planta/plantas/${user.value.id}`

    const response = await api.get(endpoint, {
      headers: { Authorization: `Bearer ${token}` }
    })
    plants.value = response.data
  } catch (error) {
    console.error('Error fetching plants:', error)
    $q.notify({
      color: 'negative',
      message: 'Erro ao carregar plantas',
      icon: 'error'
    })
  } finally {
    loading.value = false
  }
}

function getProjectTypeColor (type) {
  const colors = {
    BRIOFITA: 'forestgreen',
    PTERIDOFITA: 'limegreen',
    GIMNOSPERMA: 'darkgreen',
    ANGIOSPERMA: 'yellowgreen',
    HERBACEA: 'springgreen',
    ARBUSTO: 'olive',
    ARVORE: 'green',
    TREPADEIRA: 'seagreen',
    SUCULENTA: 'mediumseagreen',
    CACTO: 'darkolivegreen',
    ORQUIDEA: 'orchid',
    BROMELIA: 'magenta',
    AEROFITA: 'skyblue',
    HIDROFITA: 'deepskyblue',
    XEROFITA: 'sandybrown',
    LITOFITA: 'goldenrod',
    EPIFITA: 'lightblue',
    CAMPESTRE: 'greenyellow',
    RIPICOLA: 'turquoise',
    PARASITA: 'crimson',
    CARNIVORA: 'red',
    AQUATICA: 'aqua',
    LAGUNAR: 'dodgerblue',
    FLORESTAL: 'darkslategrey',
    CAMPONESA: 'peru'
  }
  return colors[type] || 'grey'
}

function openNewProjectDialog () {
  resetNewProject()
  newPlantDialog.value = true
}

function resetNewProject () {
  newPlant.value = {
    nome: '',
    nomecientifico: '',
    descricao: '',
    origem: '',
    cuidados: '',
    dataregistro: null, // ou uma data padrão, se necessário
    categoria: ''
  }
}

async function saveNewProject () {
  // Validação de campos
  if (!newPlant.value.nome || !newPlant.value.nomecientifico || !newPlant.value.descricao) {
    $q.notify({
      color: 'negative',
      message: 'Todos os campos devem ser preenchidos',
      icon: 'error'
    })
    return
  }

  try {
    const token = localStorage.getItem('userToken')
    const projectData = {
      ...newPlant.value,
      categoria: newPlant.value.categoria.value // Aqui garantimos que apenas o valor seja enviado
    }
    console.log('Plant data being sent:', projectData) // Log para depuração
    await api.post('http://localhost:8080/planta/criar', projectData, {
      headers: { Authorization: `Bearer ${token}` }
    })
    $q.notify({
      color: 'positive',
      message: 'Planta cadastrada com sucesso',
      icon: 'check'
    })
    newPlantDialog.value = false
    resetNewProject()
    await fetchProjects()
  } catch (error) {
    console.error('Error creating plant:', error.response ? error.response.data : error)
    $q.notify({
      color: 'negative',
      message: 'Erro ao criar planta: ' + (error.response?.data?.message || error.message),
      icon: 'error'
    })
  }
}

function editProject (plant) {
  // Implement edit functionality
  console.log('Edit project:', plant)
}

function confirmDeleteProject (plant) {
  projectToDelete.value = plant
  confirmDelete.value = true
}

async function deleteProject () {
  try {
    const token = localStorage.getItem('userToken')
    await api.delete(`http://localhost:8080/planta/${projectToDelete.value.id}`, {
      headers: { Authorization: `Bearer ${token}` }
    })
    $q.notify({
      color: 'positive',
      message: 'Planta excluída com sucesso',
      icon: 'check'
    })
    await fetchProjects()
  } catch (error) {
    console.error('Error deleting project:', error)
    $q.notify({
      color: 'negative',
      message: 'Erro ao excluir planta',
      icon: 'error'
    })
  }
}
</script>

<style scoped>
.project-card {
  height: 100%;
  display: flex;
  flex-direction: column;
}

.project-card .q-card__section:nth-child(2) {
  flex-grow: 1;
}
</style>
