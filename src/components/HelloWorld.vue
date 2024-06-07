<template>
  <v-card>
    <v-tabs v-model="tab" bg-color="primary">
      <v-tab>CONSULTA</v-tab>
      <v-tab>EDICIÓN</v-tab>
      <v-tab @click="newItem()">REGISTRO</v-tab>
    </v-tabs>
    <v-tabs-items v-model="tab">
      <v-tab-item>
        <!-- Tabla Ver -->
        <v-data-table :headers="headers" :items="processors" class="elevation-1" :search="search">
          <template v-slot:top>
            <v-toolbar flat>
              <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line
                hide-details></v-text-field>
              <v-divider class="mx-4" inset vertical></v-divider>
              <v-spacer></v-spacer>
            </v-toolbar>
          </template>
          <template v-slot:item.actions="{ item }">
            <v-btn depressed color="primary" @click="getItem(item)">
              Ver más
            </v-btn>
          </template>
        </v-data-table>
        <!-- Fin tabla -->
      </v-tab-item>

      <v-tab-item>
        <!-- Tabla Edicion -->
        <v-data-table :headers="headers" :items="processors" class="elevation-1" :search="search">
          <template v-slot:top>
            <v-toolbar flat>
              <v-text-field v-model="search" append-icon="mdi-magnify" label="Search" single-line
                hide-details></v-text-field>
              <v-divider class="mx-4" inset vertical></v-divider>
              <v-spacer></v-spacer>
            </v-toolbar>
          </template>
          <template v-slot:item.actions="{ item }">
            <v-btn depressed color="primary" @click="editItem(item)">
              Editar
            </v-btn>
          </template>
        </v-data-table>
        <!-- Fin Tabla edicion -->
      </v-tab-item>

      <!-- Modal -->
      <v-dialog v-model="dialog" max-width="500px">
        <v-card>
          <v-card-title>
            <span class="text-h5">{{ formTitle }}</span>
          </v-card-title>

          <v-card-text>
            <v-container>
              <v-row>
                <v-col cols="12" sm="6" md="4">
                  <v-text-field v-model="editedItem.cpu" label="Cpu" :disabled="disabledFields">
                  </v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="4">
                  <v-text-field v-model="editedItem.cores" label="Cores" :disabled="disabledFields">
                  </v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="4">
                  <v-text-field v-model="editedItem.threads" label="Threads" :disabled="disabledFields">
                  </v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="4">
                  <v-text-field v-model="editedItem.base" label="Base Clock" :disabled="disabledFields">
                  </v-text-field>
                </v-col>
                <v-col cols="12" sm="6" md="4">
                  <v-text-field v-model="editedItem.boost" label="Boost Clock" :disabled="disabledFields">
                  </v-text-field>
                </v-col>

                <v-col cols="12" sm="6" md="4">
                  <v-text-field v-model="editedItem.tdp" label="TDP(W)" :disabled="disabledFields">
                  </v-text-field>
                </v-col>
              </v-row>
            </v-container>
          </v-card-text>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" text @click="close">
              Cancelar
            </v-btn>
            <v-btn v-if="!disabledFields" color="blue darken-1" text @click="saveItem">
              Guardar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <!-- Fin Modal -->
      <!-- Notificacion -->
      <v-dialog v-model="notification" width="500">
        <v-card>
          <v-card-title :class="'text-h5 ' + notificationColor + ' lighten-1'">
            {{ notificationTitle }}
          </v-card-title>

          <v-card-text class="mt-3">
            <h2>{{ notificationMsg }}</h2>
          </v-card-text>

          <v-divider></v-divider>

          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="primary" @click="notification = false">
              Aceptar
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-dialog>
      <!-- Fin Notificación -->
    </v-tabs-items>
  </v-card>
</template>

<script>
export default {
  name: 'HelloWorld',
  data: () => ({
    tab: "one",
    search: '',
    headers: [
      {
        text: 'CPU Model',
        align: 'left',
        sortable: false,
        value: 'cpu'
      },
      { text: 'Cores', value: 'cores' },
      { text: 'Threads', value: 'threads' },
      { text: 'Base Clock', value: 'base' },
      { text: 'Boost Clock', value: 'boost' },
      { text: 'TDP(W)', value: 'tdp' },
      { text: 'Actions', value: 'actions', sortable: false }
    ],
    processors: [
      {
        cpu: 'Intel Core i9',
        cores: 8,
        threads: 16,
        base: '3.5 GHZ',
        boost: '5.4 GHZ',
        tdp: '125W'
      },
      {
        cpu: 'AMD Ryzen 9',
        cores: 16,
        threads: 32,
        base: '3.4 GHZ',
        boost: '4.9 GHZ',
        tdp: '105W'
      },
      {
        cpu: 'Intel Core i7',
        cores: 8,
        threads: 16,
        base: '3.8 GHZ',
        boost: '5.1 GHZ',
        tdp: '125W'
      },
      {
        cpu: 'AMD Ryzen 5',
        cores: 6,
        threads: 12,
        base: '3.7 GHZ',
        boost: '4.6 GHZ',
        tdp: '65W'
      },
      {
        cpu: 'Intel Core i5',
        cores: 6,
        threads: 12,
        base: '4.1 GHZ',
        boost: '4.8 GHZ',
        tdp: '125W'
      }
    ],
    dialog: false,
    editedIndex: -1,
    disabledFields: false,
    editedItem: {},
    notification: false,
    notificationTitle: '',
    notificationMsg: '',
    notificationColor: 'primary'
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Nuevo Registro' : 'Editar Registro'
    }
  },

  watch: {
    dialog(val) {
      val || this.close()
    }
  },

  methods: {
    getItem(item) {
      this.editedIndex = this.processors.indexOf(item);
      this.editedItem = Object.assign({}, item),
        this.disabledFields = true;
      this.dialog = true;
    },
    editItem(item) {
      this.editedIndex = this.processors.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.disabledFields = false;
      this.dialog = true;
    },
    newItem() {
      this.editedIndex = -1;
      let item = {
        cpu: '',
        cores: '',
        threads: '',
        base: '',
        boost: '',
        tdp: ''
      };
      this.editedItem = Object.assign({}, item);
      this.disabledFields = false;
      this.dialog = true;
    },
    close() {
      this.dialog = false;
    },
    saveItem() {
      let errors = [];
      for (let prop in this.editedItem) {
        if (this.editedItem[prop] == null || this.editedItem[prop] == '') {
          errors.push(prop);
        }
      }
      if (errors.length > 0) {
        this.notificationColor = "error";
        this.notificationMsg = 'Por favor llena los siguientes campos: ' + errors;
        this.notificationTitle = 'Error';
      } else {
        this.close();
        if (this.editedIndex > -1) {
          Object.assign(this.processors[this.editedIndex], this.editedItem);
        } else {
          this.processors.push(this.editedItem);
          this.tab = 0;
        }
        this.notificationColor = "primary";
        this.notificationMsg = 'Registro guardado' + errors;
        this.notificationTitle = 'Notificación';
      }
      this.notification = true;
    },
  }

}
</script>
