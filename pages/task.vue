<template>
  <v-main id="container">
    <h1>Gerenciador de Tarefas</h1>

    <div id="options">
      <v-row>
        <v-col
          cols="12"
          sm="12"
          md="9"
        >
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Pesquise por título, descrição ou situação"
            single-line
            hide-details
            style="margin-right:30px"
          ></v-text-field>
        </v-col>
        <v-col
          id="option-special"
          cols="12"
          sm="12"
          md="3"
        >
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
        </v-col>
      </v-row>
    </div>    
    
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
                      <v-select
                          v-show="formTitle !== 'Nova Tarefa'"
                          v-model="editedItem.status"
                          :items="items"
                          label="Situação"
                      ></v-select>
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
      <template v-slot:item.situations="{ item }">
        <v-icon
          small
          color="#e2e2e2"
          v-show="item.status=='Aberta'"
          :title="item.status"
        >
          mdi-circle-outline
        </v-icon>
        <v-icon
          small
          color="#F59921"
          v-show="item.status=='Pausada'"
          :title="item.status"
        >
          mdi-circle-slice-3
        </v-icon>
        <v-icon
          small
          color="#00ff00"
          v-show="item.status=='Fazendo'"
          :title="item.status"
        >
          mdi-circle-slice-3
        </v-icon>
        <v-icon
          small
          color="#ff0000"
          v-show="item.status=='Concluída'"
          :title="item.status"
        >
          mdi-circle-slice-8
        </v-icon>
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
      { text: 'Id', value: 'id' },
      { text: 'Dada de Criação', align: 'start', sortable: false, value: 'criationDate'},
      { text: 'Título', value: 'title' },
      { text: 'Descrição', value: 'description' },
      { text: 'Data de Conclusão', value: 'conclusionDate'},
      { text: 'Situação', value: 'situations', align: 'center', sortable: false},
      { text: 'Ações', value: 'actions', align: 'center', sortable: false},
    ],
    tasks: [],
    editedIndex: -1,
    editedItem: {
      id: ' ',
      title: '',
      description: '',
      status: '',
      conclusionDate: ''
    },
    defaultItem: {
      id: ' ',
      title: '',
      description: '',
      status: 'Aberta',
      conclusionDate: ''
    },
    //PESQUISA
    search: "",
    //REQUISIÇÃO
    urlBaseAPI: "http://localhost:8080/task/",
    idAposConfirmar: '',
    //SELECT DE SITUAÇÃO
    items: [
      'Aberta',
      'Fazendo',      
      'Pausada',
      'Concluída'
    ],
  }),

  computed: {
    formTitle () {
      return this.editedIndex === -1 ? 'Nova Tarefa' : 'Editar Tarefa'
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
        this.tasks = await this.$axios.$get(this.urlBaseAPI)
      },

      editItem (item) {
        this.editedIndex = this.tasks.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },      
     
      deleteItem (item) {  
        this.editedIndex = this.tasks.indexOf(item)  
        this.editedItem = Object.assign({}, item)  
        this.idAposConfirmar = this.editedItem.id
        this.dialogDelete = true
      },

      async deleteItemConfirm () {         
        this.closeDelete()
        await this.$axios.delete(`http://localhost:8080/task/${this.idAposConfirmar}`)
          .then(() => {         
            this.tasks.splice(this.editedIndex, 1)   
            this.color = 'success'
            this.text = 'Tarefa excluída com sucesso!'
            this.snackbar = true
          })
          .catch((error) => {
            this.color = 'error'
            this.text = 'Erro ao excluir tarefa!'
            this.snackbar = true
            console.log('erro:', error)
          })
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
          await this.$axios.patch(`http://localhost:8080/task/${this.editedItem.id}`, this.editedItem)
            .then(() => {
              Object.assign(this.tasks[this.editedIndex], this.editedItem)
              this.color = 'success'
              this.text = 'Tarefa editada com sucesso!'
              this.snackbar = true
            })
            .catch((error) => {
              this.color = 'error'
              this.text = 'Erro ao editar tarefa!'
              console.log(error)
              this.snackbar = true
            })
        } else {  
          await this.$axios.post(`http://localhost:8080/task/`, this.editedItem)
            .then(() => {
              this.color = 'success'
              this.text = 'Tarefa salva com sucesso!'
              this.snackbar = true
            })
            .catch((error) => {
              this.color = 'error'
              this.text = 'Erro ao excluir tarefa! Verifique se os dados foram preenchidos corretamente.'
              this.snackbar = true
              console.log('erro: ', error)
            })
        }
        
        this.initialize()
        this.close()
      },
    },
};
</script>

<style scoped> 
  #container {
    margin: 30px auto 60px;
    width: 95%;
  }

  #container h1{
    margin-bottom: 20px 0;
  }

  #options {
    margin-bottom: 30px;
    padding: 20px 0 20px 20px;
  }

  #options #option-special {
    display: flex;
    justify-content:right;
  }

</style>