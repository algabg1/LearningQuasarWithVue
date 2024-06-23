<template>
  <div class="user-page">
    <h1 class="title">Minha conta</h1>
    <div class="user-info">
      <div class="profile-infos">
        <div class="user-header">
          <img v-if="imageUrl" :src="imageUrl" alt="Avatar do Usuário" class="avatar" />
          <h2 class="user-name">{{ user.name }}</h2>
        </div>
        <div class="user-details">
          <div class="user-email">
            <p>Email: {{ user.email }}</p>
          </div>
          <div class="user-description">
            <p>{{ user.description }}</p>
          </div>
        </div>
        <label for="file-upload" class="file-label">
          Carregar Imagem
          <input id="file-upload" type="file" @change="onFileChange" accept="image/*" class="file-input">
        </label>
      </div>
      <div class="projects-list">
        <h3>Projetos</h3>
        <ul>
          <li v-for="project in projects" :key="project.id">
            <button @click="handleProjectClick(project.id)" class="project-button">{{ project.name }}</button>
          </li>
        </ul>
        <button @click="handleCreateProject" class="create-btn">Criar projeto</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'UserPage',
  data () {
    return {
      user: {
        name: 'Nome do Usuário',
        email: 'nome_de_usuario@gmail.com',
        description: 'Descrição do usuário'
      },
      imageUrl: '', // URL da imagem selecionada
      projects: []
    }
  },
  mounted () {
    this.fetchUserProjects()
  },
  methods: {
    async fetchUserProjects () {
      try {
        // Simulando uma requisição para buscar os projetos do usuário
        this.projects = [
          { id: 1, name: 'Projeto 1' },
          { id: 2, name: 'Projeto 2' },
          { id: 3, name: 'Projeto 3' }
        ]
      } catch (error) {
        console.error('Erro ao buscar os projetos do usuário', error)
      }
    },
    handleCreateProject () {
      console.log('Vai para a página criar projeto')
      // Adicionar aqui o redirecionamento para a página de criar projeto
    },
    handleProjectClick (projectId) {
      console.log('Projeto clicado:', projectId)
      // Adicionar aqui a lógica para redirecionar para a página do projeto clicado
    },
    onFileChange (event) {
      const file = event.target.files[0] // Obtém o arquivo selecionado
      if (file) {
        const reader = new FileReader()
        reader.onload = (e) => {
          this.imageUrl = e.target.result // Armazena a URL da imagem
        }
        reader.readAsDataURL(file) // Lê o arquivo como um Data URL
      }
    }
  }
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

.file-label {
  display: inline-block;
  margin-top: 20px;
  padding: 10px 15px;
  background-color: #afc6a6;
  color: #fff;
  border-radius: 5px;
  cursor: pointer;
}

.file-label:hover {
  background-color: #7aaf7c;
}

.file-input {
  display: none;
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
}

.create-btn:hover {
  background-color: #7aaf7c;
}
</style>
