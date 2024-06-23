<template>
  <q-page padding>
    <q-card class="create-project-card">
      <q-card-section>
        <h5 align="center">Cadastrar novo projeto</h5>
        <q-form @submit.prevent="submitForm" class="create-project-form">
          <q-input
            outlined
            v-model="project.name"
            label="Nome do Projeto"
            placeholder="Digite o nome do projeto"
            class="create-project-input"
          />
          <q-input
            outlined
            v-model="project.description"
            label="Descrição do Projeto"
            placeholder="Digite a descrição do projeto"
            class="create-project-input"
          />
          <q-select
            outlined
            v-model="project.type"
            :options="projectTypes"
            label="Tipo de Projeto"
            placeholder="Selecione o tipo de projeto"
            class="create-project-input"
          />
          <q-btn
            type="submit"
            label="Cadastrar"
            class="create-project-btn"
            :loading="isLoading"
          />
        </q-form>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'

export default {
  setup () {
    const router = useRouter()

    const project = ref({
      name: '',
      description: '',
      type: ''
    })

    const isLoading = ref(false)

    // Opções para o dropdown de Tipo de Projeto
    const projectTypes = [
      'JARDIM_BORBOLETAS',
      'HORTICULTURA',
      'PAISAGISMO',
      'HIDROPONIA',
      'JARDIM_TERAPEUTICO',
      'JARDIM_ERVAS',
      'HORTA_ORGANICA',
      'TERRARIO',
      'JARDIM_FLORES'
    ]

    const submitForm = async () => {
      isLoading.value = true
      try {
        const response = await axios.post('http://localhost:8080/projeto/criar', project.value)
        if (response.status === 201 || response.status === 200) {
          alert('Projeto cadastrado com sucesso!')
          router.push('/perfil') // redireciona para a página inicial após o cadastro
        } else {
          alert('Ocorreu um erro durante o cadastro do projeto. Tente novamente.')
        }
      } catch (error) {
        console.error('Erro ao cadastrar projeto:', error)
        alert('Ocorreu um erro durante o cadastro do projeto. Tente novamente.')
      } finally {
        isLoading.value = false
      }
    }

    return {
      project,
      projectTypes,
      isLoading,
      submitForm
    }
  }
}
</script>

<style scoped>
.create-project-card {
  max-width: 400px;
  margin: 0 auto;
}
.create-project-form {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.create-project-input {
  max-width: 300px;
  margin-bottom: 15px;
}
.create-project-btn {
  margin-top: 20px;
  transition: transform 0.2s ease-in-out;
}
.create-project-btn:active {
  transform: scale(0.95);
}
</style>
