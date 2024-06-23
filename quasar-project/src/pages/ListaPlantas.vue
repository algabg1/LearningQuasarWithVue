<template>
  <div class="container">
    <h1 class="title">Plantas Cadastradas</h1>
    <div class="plants-list">
      <div v-for="planta in plantas" :key="planta.id" class="plant-card">
        <img :src="getImageUrl(planta.imagem)" alt="Imagem da Planta" class="plant-image" v-if="planta.imagem" />
        <div class="plant-info">
          <h2>{{ planta.nome }}</h2>
          <p><strong>Nome Científico:</strong> {{ planta.nome_cientifico }}</p>
          <p><strong>Descrição:</strong> {{ planta.descricao }}</p>
          <p><strong>Origem:</strong> {{ planta.origem }}</p>
          <p><strong>Cuidados:</strong> {{ planta.cuidados }}</p>
          <p><strong>Data de Registro:</strong> {{ formatDate(planta.dataregistro) }}</p>
          <p><strong>Categoria:</strong> {{ planta.categoria }}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'MostrarPlantas',
  data () {
    return {
      plantas: []
    }
  },
  created () {
    this.fetchPlantas()
  },
  methods: {
    async fetchPlantas () {
      try {
        const response = await axios.get('http://localhost:8080/planta/plantas')
        this.plantas = response.data
      } catch (error) {
        console.error('Erro ao buscar plantas:', error)
      }
    },
    getImageUrl (imagem) {
      if (imagem) {
        return `data:image/jpeg;base64,${imagem}`
      }
      return null
    },
    formatDate (dateString) {
      const date = new Date(dateString)
      return date.toLocaleDateString()
    }
  }
}
</script>

<style scoped>
.container {
  background: #AFC6A6;
  min-height: 100vh;
  padding: 2rem;
}

.title {
  font-size: 3rem;
  color: #000;
  text-align: center;
  margin-bottom: 2rem;
}

.plants-list {
  display: flex;
  flex-wrap: wrap;
  gap: 2rem;
  justify-content: center;
}

.plant-card {
  background: #fff;
  padding: 1rem;
  border-radius: 8px;
  box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px;
  width: 300px;
  text-align: center;
}

.plant-image {
  border-radius: 8px;
  width: 100%;
  height: auto;
  margin-bottom: 1rem;
}

.plant-info {
  h2 {
    font-size: 1.5rem;
    color: #333;
  }

  p {
    font-size: 1rem;
    color: #666;
  }
}
</style>
