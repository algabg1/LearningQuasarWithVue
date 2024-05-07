<template>
  <div class="principal">
    <div class="profile-infos">
      <h2> {{ userProfile.name }} </h2>
      <p> {{ userProfile.email }}</p>
    </div>
    <div class="projects-list">
      <h3>Projetos</h3>
      <ul>
        <li v-for="project in projects" :key="project.id">{{ project.name }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      userProfile: {
        name: '',
        email: ''
      },
      projects: []
    }
  },
  mounted () {
    this.userProfile = {
      name: 'Nome do Usuário',
      email: 'nome_de_usuario'
    }
    this.fetchUserProjects()
  },
  methods: {
    async fetchUserProjects () {
      try {
        const response = await fetch(`/api/v2/projects/${this.projects.name}`)
        const data = await response.json()
        this.projects = data.projects
      } catch (error) {
        console.error('Erro ao buscar os projetos do usuário', error)
      }
    }
  }
}
</script>

<style scoped>
  .profile-infos {
    background-color: var(--select-background);
    z-index: -1;
    border-radius: 8px;
    padding: 1%;
    display: flex;
    flex-direction: column;
    align-items: left;
    z-index: 1
  }

  .principal {
    padding: 5%;
  }
</style>
