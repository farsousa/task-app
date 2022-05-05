<template>
  <v-main>
    <div id="container">
      
        <img src="construction-image.png" alt="sistemamanutencao" />       
       
      <div id="container-text">
        <h1>Em construção</h1>
        <p>A tela de login está em construção para que você possa salvar permanentemente suas tarefas.</p>
        <NuxtLink to="/"><button>Voltar ao Início</button></NuxtLink> 
      </div>  
    </div>
  </v-main>
</template>

<script>
export default {
  name: 'LoginPage',
}
</script>

<style scoped>
#container {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    width: 60%;
    min-width: 200px;
    margin: 40px auto;
  }

  #container-image img{
    min-width: 300px;
    max-width: 30%;
  }

  #container-text {
    min-width: 300px;
    width: 30%;
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
    transition-property: transform;
    transition-duration: 0.2s;
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
