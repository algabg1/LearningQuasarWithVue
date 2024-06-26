<template>
  <div class="user-page">
    <h1 class="title">Minha conta</h1>
    <div class="user-info">
      <div class="profile-infos">
        <div class="user-header">
          <h2 class="user-name">{{ user.nome }}</h2>
        </div>
        <div class="user-details">
          <div class="user-email">
            <p>Email: {{ user.email }}</p>
          </div>
          <div class="user-description">
            <p>Perfil: {{ user.user_role }}</p>
          </div>
        </div>
      </div>
      <div class="projects-list">
        <h3>Projetos</h3>
        <ul>
          <li v-for="project in projects" :key="project.id">
            <button @click="handleProjectClick(project.id)" class="project-button">{{ project.name }}</button>
          </li>
        </ul>
        <div v-if="user.user_role === 'ADMIN'" class="button-group">
          <button @click="handleCreateProject" class="create-btn">Criar projeto</button>
          <button @click="handleCreatePlant" class="create-btn">Cadastrar planta</button>
        </div>
        <div v-else class="button-group">
          <button @click="handleCreateProject" class="create-btn">Criar projeto</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'

const user = ref({
  nome: '',
  email: '',
  description: '',
  role: ''
})

const projects = ref([])

const router = useRouter()

onMounted(() => {
  fetchUserData()
  fetchUserProjects()
})

function fetchUserData () {
  const userData = localStorage.getItem('userData')
  if (!userData) {
    router.push('/login')
  } else {
    user.value = JSON.parse(userData)
    fetchUserRole()
  }
}

function fetchUserRole () {
  axios.get('/user')
    .then(response => {
      const userRole = response.data.role
      user.value.role = userRole // Atualiza o papel do usuário com os dados do backend
    })
    .catch(error => {
      console.error('Erro ao obter o papel do usuário:', error)
    })
}

async function fetchUserProjects () {
  try {
    // Simulando uma requisição para buscar os projetos do usuário
    projects.value = [
      { id: 1, name: 'Projeto 1' },
      { id: 2, name: 'Projeto 2' },
      { id: 3, name: 'Projeto 3' }
    ]
  } catch (error) {
    console.error('Erro ao buscar os projetos do usuário', error)
  }
}

function handleCreateProject () {
  console.log('Vai para a página criar projeto')
  router.push('/cadastrar-projeto')
}

function handleProjectClick (projectId) {
  console.log('Projeto clicado:', projectId)
  router.push('/projetos')
}

function handleCreatePlant () {
  console.log('Vai para a página cadastrar planta')
  router.push('/cadastrar-planta')
}
</script>

<style scoped>
.title {
  font-size: 3rem;
  color: #333;
  text-align: center;
  margin-bottom: 20px;
}

.user-page {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.user-info {
  display: flex;
  justify-content: space-between;
  width: 100%;
  max-width: 1200px;
}

.profile-infos {
  background-color: #f7f7f7;
  border-radius: 8px;
  padding: 20px;
  width: 45%;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.user-header {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.avatar {
  border-radius: 50%;
  width: 100px;
  height: 100px;
  margin-right: 20px;
}

.user-name {
  font-size: 1.8rem;
}

.user-details {
  margin-bottom: 20px;
}

.user-email p,
.user-description p {
  margin: 5px 0;
}

.projects-list {
  background-color: #f7f7f7;
  border-radius: 8px;
  padding: 20px;
  width: 45%;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.projects-list h3 {
  margin-bottom: 5px;
}

.projects-list ul {
  list-style-type: none;
  padding: 0;
}

.projects-list li {
  margin-bottom: 5px;
}

.project-button {
  background-color: #afc6a6;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 10px 15px;
  cursor: pointer;
}

.project-button:hover {
  background-color: #7aaf7c;
}

.create-btn {
  background-color: #afc6a6;
  color: #fff;
  border: none;
  border-radius: 5px;
  padding: 10px 65px;
  cursor: pointer;
  margin-right: 10px;
}

.create-btn:hover {
  background-color: #7aaf7c;
}

.button-group {
  display: flex;
  justify-content: space-between;
  margin-top: 20px;
}
</style>
