<template>  
  <v-app>
    <div>
      <header id="header-page">
        <div id="header-logo">
          <NuxtLink to="/"> 
            <NuxtLogo />          
          </NuxtLink>
        </div>
        <div id="header-menu">
          <v-switch
            @change="toggleDarkTheme()"
            color="#fff"
            inset
            label="Tema Dark"
            id="teste"
          ></v-switch>
          <NuxtLink to="/login" style="text-decoration:none; margin-left: 20px"> 
            <v-btn
              color="#fff"
              dark
              class="mb-2 me-4 black--text"            
            >
              Entrar
            </v-btn>
          </NuxtLink>            
        </div>
      </header>
      <main>
        <Nuxt />
      </main>
      <footer id="footer-page">
        <span>Desenvolvido por FARSousa</span>
      </footer>
    </div>
  </v-app>
</template>

<script>
export default {
  name: 'DefaultLayout',
  data: () => ({
    theme: false,
  }),

  created() {
    this.$vuetify.theme.dark = false
  },

  methods: {
    toggleDarkTheme() {
      this.$vuetify.theme.dark = !this.$vuetify.theme.dark;
    },
  }
}
</script>

<style scoped>
  #header-page {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px 30px;
    background-color: #0563c2;
    color: #ffffff;
  }

  #header-page #header-menu {
    display: flex;
    align-items: center;
    justify-content: center;
  }

  #footer-page {
    background-color: #0563c2;
    padding: 10px 0;
    color: #ffffff;
    text-align: center;
    position: fixed;
    bottom: 0;
    width: 100%;
  }
</style>
