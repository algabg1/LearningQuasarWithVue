<template>
  <div class="container">
    <h1 class="title">Cadastrar Planta</h1>
    <div class="form-container">
      <form @submit.prevent="submitForm">
        <img v-if="imageUrl" :src="imageUrl" alt="Imagem da Planta" class="avatar" />
        <input type="file" @change="onFileChange" accept="image/*" />
        <input v-model="planta.nome" placeholder="Nome planta" type="text" required>
        <input v-model="planta.nome_cientifico" placeholder="Nome científico" type="text" required>
        <textarea v-model="planta.descricao" placeholder="Descrição"></textarea>
        <input v-model="planta.origem" placeholder="Origem" type="text">
        <input v-model="planta.cuidados" placeholder="Cuidados" type="text">
        <select v-model="planta.categoria" required>
          <option value="" disabled selected>Selecione a categoria</option>
          <option v-for="categoria in categorias" :key="categoria" :value="categoria">{{ categoria }}</option>
        </select>
        <button type="submit" class="btn-submit">Adicionar Planta</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'CadastrarPlanta',
  data () {
    return {
      planta: {
        nome: '',
        nome_cientifico: '',
        descricao: '',
        origem: '',
        cuidados: '',
        categoria: ''
      },
      imageUrl: '',
      categorias: [
        'BRIOFITA', 'PTERIDOFITA', 'GIMNOSPERMA', 'ANGIOSPERMA', 'HERBACEA',
        'ARBUSTO', 'ARVORE', 'TREPADEIRA', 'SUCULENTA', 'CACTO',
        'ORQUIDEA', 'BROMELIA', 'AEROFITA', 'HIDROFITA', 'XEROFITA',
        'LITOFITA', 'EPIFITA', 'CAMPESTRE', 'RIPICOLA', 'PARASITA',
        'CARNIVORA', 'AQUATICA', 'LAGUNAR', 'FLORESTAL', 'CAMPONESA'
      ]
    }
  },
  methods: {
    onFileChange (event) {
      const file = event.target.files[0]
      if (file) {
        const reader = new FileReader()
        reader.onload = (e) => {
          this.imageUrl = e.target.result
        }
        reader.readAsDataURL(file)
        this.planta.imagem = file
      }
    },
    submitForm () {
      // Verifica se o usuário é ADMIN
      if (this.$store.state.user.user_role !== 'ADMIN') {
        console.error('Apenas usuários com papel ADMIN podem cadastrar plantas.')
        return
      }

      // Cria um FormData para enviar a requisição
      const formData = new FormData()
      Object.keys(this.planta).forEach(key => {
        formData.append(key, this.planta[key])
      })

      // Adiciona o token de autenticação no header
      const token = localStorage.getItem('userToken')
      const config = {
        headers: {
          'Content-Type': 'multipart/form-data',
          Authorization: `Bearer ${token}`
        }
      }

      // Faz a requisição POST para cadastrar a planta
      axios.post('http://localhost:8080/planta/criar', formData, config)
        .then(response => {
          console.log('Planta cadastrada com sucesso:', response.data)
          // Exibe a mensagem de sucesso utilizando QSnackbar
          this.$q.notify({
            color: 'positive',
            message: 'Planta cadastrada com sucesso!',
            icon: 'check',
            position: 'top',
            timeout: 2000 // Tempo de exibição da mensagem (2 segundos)
          })
          // Limpar campos ou redirecionar para página de sucesso
          this.resetForm()
        })
        .catch(error => {
          console.error('Erro ao cadastrar planta:', error)
          // Exibe mensagem de erro
          this.$q.notify({
            color: 'negative',
            message: 'Erro ao cadastrar planta.',
            icon: 'warning',
            position: 'top',
            timeout: 2000 // Tempo de exibição da mensagem (2 segundos)
          })
        })
    },
    resetForm () {
      // Limpa os campos do formulário
      this.planta.nome = ''
      this.planta.nome_cientifico = ''
      this.planta.descricao = ''
      this.planta.origem = ''
      this.planta.cuidados = ''
      this.planta.categoria = ''
      this.imageUrl = ''
    }
  }
}
</script>

<style scoped>
.container {
  background: #AFC6A6;
  min-height: 100vh;
}

.title {
  font-size: 3rem;
  color: #000;
  text-align: center;
}

.form-container {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 1rem;

  form {
    display: flex;
    flex-direction: column;
    gap: 1rem;

    input, select, textarea {
      background: #D9D9D9;
      width: 400px;
      padding: 1rem;
      border: none;
      box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px;
      border-radius: 2px;
    }

    select {
      cursor: pointer;
    }
  }

  .btn-submit {
    background-color: #4caf50;
    color: #fff;
    border: none;
    padding: 10px 20px;
    border-radius: 4px;
    cursor: pointer;
    transition: background-color 0.3s ease;

    &:hover {
      background-color: #45a049;
    }
  }

  .avatar {
    border-radius: 50%;
    width: 150px;
    height: 150px;
  }
}
</style>
