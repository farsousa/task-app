<template>
  <div id="container">
    <div id="container-image">
      <img src="start-image.svg" alt="pessoasfazendoanotacoes">
    </div>
    <div id="container-text">
      <p>Com nossa ferramenta, você pode acompanhar o progresso de suas atividade de forma simples.</p>
      <button>Experimentar</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'IndexPage'
}
</script>
<style scoped>
  #container {
    display: flex;
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
  }

  @media screen and (max-width: 650px) {

    #container {
      width: 100%;
      flex-direction: column;
    }

  }
</style>
