<template>
  <div>
    <v-row justify="center">
      <v-col cols="12" sm="8" md="12" xl="12">
        <v-card class="elevation-12">
          <v-toolbar dark color="primary">
            <v-toolbar-title>MODULO DE SOLICITUDES APROBADAS</v-toolbar-title>
          </v-toolbar>
          <v-card-text>
            <v-data-table
              ref="tabla"
              :loading="loadingTabla"
              :headers="headers"
              :items="solicitudes"
              :search="search"
              sort-by="nombre"
              class="elevation-1"
            >
             <template v-slot:top>
                <v-toolbar flat>
                  <v-text-field
                    v-model="search"
                    append-icon="mdi-magnify"
                    label="Buscar"
                    single-line
                    hide-details
                  ></v-text-field>
                </v-toolbar>
             </template>
              <template v-slot:item.actions="{ item }">
                <v-icon small class="mr-2" @click="redireccionar(item.id)">
                  mdi-pencil
                </v-icon>
              </template>
              <template v-slot:no-data>
                <v-btn color="primary" @click="initialize"> Resetear </v-btn>
              </template>
            </v-data-table>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>
<script>
export default {
  data: () => ({
    solicitudes:[],
    valid: true,
    search: "",
    dialog: false,
    loadingTabla: true,
    dialogDelete: false,
    headers: [

      {
        text: "Número Documento",
        align: "start",
        sortable: false,
        value: "num_doc",
      },
      {
        text: "Estado",
        align: "start",
        sortable: false,
        value: "estado_solicitud",
      },
      { text: "Acciones", value: "actions", sortable: false },
    ],
    editedIndex: -1,
    editedItem: {
      id: "",
      name: "",
      user_name: "",
      email: "",
      password: "",
      perfil_id: "",
      estado: "",
    },
  }),
  async asyncData({ $http }) {
    try {
      const res = await $http.get("solicitudes/aprobadas");
      const solicitudes = await res.json();
      return { solicitudes: solicitudes.data };
    } catch (error) {
      return { error };
    }
  },
  async created() {
    this.loadingTabla = false;
  },

    beforeCreate() {
    let id_usuario = localStorage.getItem("user_id");
    let perfil_id = localStorage.getItem("perfil_id");
    console.log(id_usuario);
    if ((id_usuario == null)) {
      window.$nuxt.$router.push("/");
    }
    if ((perfil_id != 2)) {
      window.$nuxt.$router.push("/home");
    }
  },

  methods: {
    redireccionar(id) {
      window.$nuxt.$router.push("/solicitudes/"+id);
    },

    async initialize() {
      try {
        let res = await this.$axios.get("solicitudes/aprobadas")
        this.solicitudes = await res.data.data
      } catch (error) {
        console.log(error)
      }

    },
  },
};
</script>
