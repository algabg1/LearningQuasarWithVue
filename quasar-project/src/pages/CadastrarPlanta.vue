<template>
  <div class="container">
    <h1 class="title">Cadastrar Planta</h1>
    <div class="form-container">
      <form>
        <img v-if="imageUrl" :src="imageUrl" alt="Avatar do Usuário" class="avatar" />
        <input type="file" @change="onFileChange" accept="image/*" />
        <input placeholder="Nome planta" type="text">
        <input placeholder="Adicionar Informações" type="text">
        <button type="button" class="btn-submit">Adicionar aos meus projetos</button>
      </form>

    </div>

  </div>
</template>

<script>
export default {
  name: 'CadastrarPlanta',
  data () {
    return {
      imageUrl: ''
    }
  },
  methods: {
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

    input {
      background: #D9D9D9;
      width: 400px;
      padding: 1rem;
      border: none;
      box-shadow: rgba(0, 0, 0, 0.1) 0px 10px 15px -3px, rgba(0, 0, 0, 0.05) 0px 4px 6px -2px;
      border-radius: 2px;
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
