<template>
  <v-main>
    <div id="container">
      <div id="container-image">
        <img src="start-image.svg" alt="pessoasfazendoanotacoes" />
      </div>
      <div id="container-text">
        <h1>Gerenciador de Tarefas</h1>
        <p>Com nossa ferramenta, você pode acompanhar o progresso de suas atividade de forma simples.</p>
        <NuxtLink to="/task"><button>Experimentar Grátis</button></NuxtLink> 
      </div>
    </div>
  </v-main>  
</template>

<script>
export default {
  name: 'IndexPage'
}
</script>

<style scoped>
  #container {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
  }

  #container-image {
    min-width: 300px;
    width: 60%;
  }

  #container-text {
    min-width: 300px;
    width: 40%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
  }

  #container-text h1 {
    text-align: center;
    margin-bottom: 20px;
  }

  #container-text p {
    text-align: center;
    padding: 0 15px;
  }

  #container-text button {
    background-color: #0563c2;
    color: #ffffff;
    padding: 10px 20px;
    border-radius: 8px;
    margin-top: 30px;
    width: 50%;
    min-width: 250px;
    font-weight: bold;    
  }

  #container-text button:hover {
    transform: scale(1.1);
  }

  @media screen and (max-width: 650px) {

    #container {
      width: 100%;
      flex-direction: column;
    }

  }
</style>
