<template>  
  <div>
    <header id="header-page">
      <div id="logo">
        <NuxtLink to="/"> 
          <NuxtLogo id="page"/>          
        </NuxtLink>
      </div>
      <div id="menus">
        <NuxtLink to="/" class="menu-link"> 
          <span>Início</span>
        </NuxtLink>
        <NuxtLink to="/tasks" class="menu-link"> 
          <span>Tarefas</span> 
        </NuxtLink>               
      </div>
    </header>
    <main id="main-page">
      <Nuxt />
    </main>
    <footer id="footer-page">
      Todos os direitos reservados
    </footer>
  </div>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data () {
    return {
      
    }
  }
}
</script>

<style scoped>
  #header-page {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 5px 30px;
    background-color: #0563c2;
    color: #ffffff;
  }

  #header-page #logo {
    widows: 10px;
    display: flex;
    align-items: center;
    font-size: 1.3em;
  }

  #header-page #menus {
    font-size: 1.3em;
  }

  #header-page #menus .menu-link {
    text-decoration: none;
    color: #ffffff;
  }

  #header-page #menus span{
    margin-left: 10px;
  }

  #main-page {
    margin: 30px;
  }

  #footer-page {
    background-color: #0563c2;
    color: #ffffff;
    text-align: center;
    position: fixed;
    bottom: 0;
    width: 100%;
  }

  @media screen and (max-width: 650px) {
    #main-page {
      margin: 0px;
    }
  }
</style>
