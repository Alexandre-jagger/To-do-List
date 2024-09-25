<template>
  <v-app :class="isDark">
    <v-app-bar app color="primary" dark>
      <v-toolbar-title>Gerenciador de Tarefas</v-toolbar-title>
      <v-spacer></v-spacer>

      <v-btn @click="toggleTheme" icon>
        <v-icon>{{ isDark ? 'mdi-brightness-5' : 'mdi-brightness-2' }}</v-icon>
      </v-btn>

      <router-link to="/rudComponent">
        <v-btn color="secondary" class="ml-2">Ir para CRUD</v-btn>
      </router-link>
    </v-app-bar>

    <v-main>
      <v-container>
        <v-card class="mx-auto" max-width="700">
          <v-card-title class="mt-10">
            <span class="headline">Lista de Tarefas</span>
          </v-card-title>

          <v-card-text>
            <v-form ref="form" v-model="valido">
              <v-text-field
                v-model="novoItem"
                label="Adicionar nova tarefa"
                :rules="[rules.required]"
                outlined
                maxlength="30"
              ></v-text-field>

              <v-btn @click="adicionarItem" :disabled="!valido || items.length >= 15" color="primary">
                Adicionar
              </v-btn>
            </v-form>

            <v-divider :thickness="10"></v-divider>

            <v-row class="mt-4">
              <v-col>
                <v-subheader>Tarefas Totais: {{ items.length }}</v-subheader>
              </v-col>
              <v-col>
                <v-subheader>Tarefas Concluídas: {{ completoCount }}</v-subheader>
              </v-col>
            </v-row>

            <v-list class="mt-3 scrollable-list">
              <v-list-item-group>
                <v-list-item v-for="item in items" :key="item.id">
                  <v-list-item-content>
                    <v-list-item-title>
                      <div class="task-item">
                        <v-checkbox
                          v-model="item.completo"
                          @change="updateCompletoCount"
                          class="checkbox-wrapper"
                        ></v-checkbox>

                        <v-text-field
                          v-model="item.text"
                          :disabled="!item.editando"
                          class="task-text"
                        ></v-text-field>

                        <v-btn icon @click="toggleEdit(item.id)" class="edit-btn-wrapper">
                          <v-icon>{{ item.editando ? 'mdi-check' : 'mdi-pencil' }}</v-icon>
                        </v-btn>

                        <v-btn icon @click="removerItem(item.id)" class="delete-btn-wrapper">
                          <v-icon>mdi-delete</v-icon>
                        </v-btn>
                      </div>
                    </v-list-item-title>
                  </v-list-item-content>
                </v-list-item>
              </v-list-item-group>
            </v-list>
          </v-card-text>
        </v-card>
      </v-container>
    </v-main>

    <v-snackbar v-model="snackbar" :timeout="1400">
      {{ snackbarMessage }}
      <v-btn color="red" text @click="snackbar = false">Fechar</v-btn>
    </v-snackbar>
  </v-app>
</template>

<script>
export default {
  name: 'App',

  data() {
    return {
      novoItem: '', 
      items: [],
      valido: false,
      rules: {
        required: value => !!value || 'campo obrigatório',
      },
      isDark: false,
      snackbar: false,
      snackbarMessage: '',
    };
  },

  computed: {
    completoCount() {
      return this.items.filter(item => item.completo).length;
    },
  },

  methods: {
    adicionarItem() { 
      if (this.novoItem && this.items.length < 15) {
        const novaTarefa = { id: Date.now(), text: this.novoItem, completo: false, editando: false }; 
        this.items.push(novaTarefa);
        this.novoItem = '';
      }
    },
    
    removerItem(id) {
      const index = this.items.findIndex(item => item.id === id);
      if (index !== -1) {
        const removeItem = this.items[index].text;
        this.items.splice(index, 1);
        this.snackbarMessage = `item "${removeItem}" excluido`;
        this.snackbar = true;
      }
    },
    
    // contagem tarefas
    updateCompletoCount() {
      this.completoCount = this.items.filter(item => item.completo).length;
    },

    //altera tema
    toggleTheme() {
      this.isDark = !this.isDark;
      this.$vuetify.theme.dark = this.isDark;
    },
    
    // alterar
    toggleEdit(id) {
      const item = this.items.find(item => item.id === id);
      if (item) {
        item.editando = !item.editando;
      }
    },
  },
};
</script>

<style>
.scrollable-list {
  max-height: 150px;
  overflow-y: auto;
}

.task-item {
  display: flex;
  align-items: center;
}

.task-text {
  width: 300px;
  margin-right: 10px;
  border: none;
}

.checkbox-wrapper {
  position: relative;
  top: -15px;
}

.edit-btn-wrapper,
.delete-btn-wrapper {
  position: relative;
  top: -15px;
}
</style>
