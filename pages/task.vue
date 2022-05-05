<template>
  <v-main id="container">
    <h1>Gerenciador de Tarefas</h1>
    
    <div class="text-center">
      <v-snackbar
        v-model="snackbar"
        :multi-line="multiLine"
        :color="color"
      >
        {{ text }}

        <template v-slot:action="{ attrs }">
          <v-btn
            color="black"
            text
            v-bind="attrs"
            @click="snackbar = false"
          >
            Fechar
          </v-btn>
        </template>
      </v-snackbar>
    </div> 

    <v-data-table
      :headers="headers"
      :items="tasksWithFilter"
      sort-by="calories"
      class="elevation-1"
    >
      <template v-slot:top>
        <v-toolbar
          flat
        >
          <v-spacer></v-spacer>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Pesquise por título, descrição ou status"
            single-line
            hide-details
            style="margin-right:30px"
          ></v-text-field>
          <NuxtLink to="/">
            <v-btn
              color="primary"
              dark
              class="mb-2 me-4 scale"
            >
              Voltar
            </v-btn>
          </NuxtLink>          
          <v-btn
            color="#F59921"
            dark
            class="mb-2 me-4 scale"
            @click="search = ''"
          >
            Limpar
          </v-btn>
          <v-dialog
            v-model="dialog"
            max-width="500px"
          >
            <template v-slot:activator="{ on, attrs }">
              <v-btn
                color="success"
                dark
                class="mb-2 scale"
                v-bind="attrs"
                v-on="on"
              >
                Novo
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="editedItem.title"
                        label="Título"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="editedItem.description"
                        label="Descrição"
                      ></v-text-field>
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="editedItem.status"
                        label="Situação"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                  color="blue darken-1"
                  text
                  @click="close"
                >
                  Cancelar
                </v-btn>
                <v-btn
                  color="blue darken-1"
                  text
                  @click="save"
                >
                  Salvar
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5">Você tem certeza que deseja excluir este item?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete">Cancelar</v-btn>
                <v-btn color="blue darken-1" text @click="deleteItemConfirm">Ok</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon
          small
          class="mr-2"
          @click="editItem(item)"
        >
          mdi-pencil
        </v-icon>
        <v-icon
          small
          @click="deleteItem(item)"
        >
          mdi-delete
        </v-icon>
      </template>
      <template v-slot:no-data>
        Dados não encontrados
      </template>
    </v-data-table>
  </v-main>  
</template>

<script>
export default {
  name: 'TaskPage',
  data: () => ({
    //SNACKBAR
    multiLine: true,
    snackbar: false,
    text: '',
    color: '',
    //TABELA
    dialog: false,
    dialogDelete: false,
    headers: [
      { text: 'Dada de Criação', align: 'start', sortable: false, value: 'criationDate'},
      { text: 'Título', value: 'title' },
      { text: 'Descrição', value: 'description' },
      { text: 'Situação', value: 'status' },
      { text: 'Data de Conclusão', value: 'conclusionDate' },
      { text: 'Ações', value: 'actions', align: 'right', sortable: false},
    ],
    tasks: [],
    editedIndex: -1,
    editedItem: {
      title: '',
      description: "",
      status: "",
    },
    defaultItem: {
      title: " ",
      description: " ",
      status: " ",
    },
    //PESQUISA
    search: "",
    //REQUISIÇÃO
    urlBaseAPI: "http://localhost:8080/task/"
  }),

  computed: {
    formTitle () {
      return this.editedIndex === -1 ? 'Novo Tarefa' : 'Editar Tarefa'
    },
    tasksWithFilter() {
        if (this.search){
            let exp = new RegExp(this.search.trim(), 'i')
            return this.tasks.filter(task => exp.test([task.title, task.description, task.status]))
        }else{
            return this.tasks
        }
    }
  },

  watch: {
    dialog (val) {
      val || this.close()
    },
    dialogDelete (val) {
      val || this.closeDelete()
    },
  },
  
  created () {
    this.initialize()
  },

    methods: {
      async initialize () {    
        this.search = "" 
        this.tasks = [
          {
            title: 'arrow function',
            description: 'conteúdo de js',
            status: 'Fazendo'
          },
          {
            title: 'JUnit',
            description: 'testar aplicação java',
            status: 'Feito'
          },
          {
            title: 'paginação',
            description: 'como é feito no spring boot',
            status: 'Fazer'
          },
        ]
        //const tasks = await this.$axios.$get(this.urlBaseAPI)

        /*
        this.$http.get(`${this.rotaApi}ponto/`)
            .then(res => res.json())
            .then(itens => this.pontos = itens);*/
      },

      editItem (item) {
        this.editedIndex = this.tasks.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },      
     
      deleteItem (item) {
        this.editedIndex = this.tasks.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.color = 'success'
        this.text = 'Tarefa excluída com sucesso!'
        this.snackbar = true
        //this.$http.delete(`${this.rotaApi}ponto/${item.id}`)
        this.dialogDelete = true
      },

      deleteItemConfirm () {
        this.tasks.splice(this.editedIndex, 1)
        this.closeDelete()
      },
      
      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },
      
      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },
      
      async save () {
        if (this.editedIndex > -1) {
          this.color = 'success'
          this.text = 'Tarefa editada com sucesso!'
          this.snackbar = true
          Object.assign(this.tasks[this.editedIndex], this.editedItem)
          /*await this.$http.patch(`${this.rotaApi}ponto/${this.editedItem.id}`, this.editedItem).then(() => {
            this.infoalert = 'success'
            this.textalert = 'Filiado editado com sucesso!'
            Object.assign(this.pontos[this.editedIndex], this.editedItem)
            setTimeout(() => {this.textalert = ''}, 3000)
          },
          () => {
            this.infoalert = 'error'
            this.textalert = 'Erro ao editar filiado, verifique se os campos obrigatórios (*) foram preenchidos!'
            setTimeout(() => {this.textalert = ''}, 4000)
          })*/
        } else {  
          this.color = 'success'
          this.text = 'Tarefa salva com sucesso!'
          this.snackbar = true
          this.tasks.push(this.editedItem)        
          /*await this.$http.post(`${this.rotaApi}ponto/`, this.editedItem).then(() => {
            this.infoalert = 'success'
            this.textalert = 'Filiado salvo com sucesso!'
            this.pontos.push(this.editedItem)
            setTimeout(() => {this.textalert = ''}, 3000)
          },
          () =>{
            this.infoalert = 'error'
            this.textalert = 'Erro ao salvar filiado, verifique se os campos obrigatórios (*) foram preenchidos!'
            setTimeout(() => {this.textalert = ''}, 4000)
          })*/
        }
        this.close()
      },
    },
};
</script>

<style scoped> 
  #container {
    margin: 30px auto;
    width: 95%;
  }

  #container h1{
    margin-bottom: 20px;
  }
</style>