<template>
  <div>
    <v-row justify="center" align="center" v-if="!mostrar">
      <v-col cols="12" sm="8" md="10">
        <v-card class="elevation-12">
          <v-toolbar dark color="primary">
            <v-toolbar-title>DATOS DE SOLICITUD</v-toolbar-title>
          </v-toolbar>
          <v-card-text>
            <v-form ref="form" v-model="valid" lazy-validation>
              <v-simple-table>
                <template v-slot:default>
                  <tbody>
                    <tr>
                      <th>Nombre</th>
                      <td>{{ solicitud.full_name }}</td>
                    </tr>
                    <tr>
                      <th>Número Documento</th>
                      <td>{{ solicitud.num_doc }}</td>
                    </tr>
                    <tr>
                      <th>Fecha Nacimiento</th>
                      <td>{{ solicitud.fecha_nac }}</td>
                    </tr>
                    <tr>
                      <th>Correo</th>
                      <td>{{ solicitud.correo }}</td>
                    </tr>
                    <tr>
                      <th>Teléfono</th>
                      <td>{{ solicitud.telefono }}</td>
                    </tr>
                    <tr>
                      <th>Empresa</th>
                      <td>{{ solicitud.empresa }}</td>
                    </tr>
                    <tr>
                      <th>ARL</th>
                      <td>{{ solicitud.arl }}</td>
                    </tr>
                    <tr>
                      <th>EPS</th>
                      <td>{{ solicitud.eps }}</td>
                    </tr>
                    <tr>
                      <th>PENSION</th>
                      <td>{{ solicitud.pension }}</td>
                    </tr>
                    <tr>
                      <th colspan="2"><center>DOCUMENTOS CARGADOS</center></th>
                    </tr>
                    <tr v-for="(item, i) in documentos"
                        :key="i">
                        <td>{{ item.nombre }}</td>
                        <td>
                          <a :href="item.ruta" target="_blank">
                          <v-icon aria-hidden="false">
                            mdi-eye
                          </v-icon>
                           Ver</a>
                        </td>
                    </tr>
                    <tr>
                      <th>ESTADO</th>
                      <td >
                        <v-select
                          v-model="editedItem.estado_solicitud"
                          :items="options"
                          label="Estado"
                          :rules="[(v) => !!v || 'Debe seleccionar una opcion']"
                          required
                          single-line
                          outlined
                        ></v-select>
                      </td>
                    </tr>
                    <tr v-if="editedItem.estado_solicitud=='RECHAZAR'">
                      <th>MOTIVOS</th>
                      <td >
                        <v-textarea
                          v-model="editedItem.motivo"
                          outlined
                          name="input-7-4"
                          label=""
                          required
                          :rules="[(v) => !!v || 'Debe seleccionar una opcion']"
                        ></v-textarea>
                      </td>
                      <td><p>
                          {{ editedItem.motivos }}
                        </p></td>
                    </tr>
                  </tbody>
                </template>
              </v-simple-table>
            </v-form>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn
              :disabled="!valid"
              color="success"
              block
              class="mr-4"
              @click="store"
            >
              CONTINUAR
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>
<script>
export default {
  data: () => ({
    editedItem: {
      solicitud_id: null,
      estado_solicitud: null,
      motivo: "",
    },
    options: ["APROBAR", "RECHAZAR"],
    valid: true,
    regla: [(v) => !!v || "Campo es obligatorio"],
    email: "",
    emailRules: [
      (v) => !!v || "Correo es obligatorio",
      (v) => /.+@.+\..+/.test(v) || "Correo no es valido",
    ],
  }),

  async asyncData({ $http, params }) {
    const res = await $http.get("solicitudes/" + params.id);
    const solicitud = await res.json();
    return { solicitud: solicitud.data, documentos: solicitud.documentos };
  },

  mounted() {
    let id_usuario = localStorage.getItem("user_id");
    let perfil_id = localStorage.getItem("perfil_id");
    console.log(id_usuario);
    if (id_usuario == null) {
      window.$nuxt.$router.push("/");
    }
    if ((perfil_id != 3)) {
      window.$nuxt.$router.push("/home");
    }
  },

  methods: {
    async store() {
      if (!this.$refs.form.validate()) {
        return;
      }
      this.editedItem.solicitud_id=this.solicitud.id;
      try {
        let datos = await this.$http.$post("solicitudes/estado", this.editedItem);
        if (datos.status == "success") {
          window.$nuxt.$swal.fire({
            text: datos.data,
            icon: datos.status,
            confirmButtonText: "Aceptar",
          });
          window.$nuxt.$router.push("/solicitudes");
        } else {
          window.$nuxt.$swal.fire({
            text: "Se ha presentado un error al registrar los datos",
            icon: datos.status,
            confirmButtonText: "Aceptar",
          });
          return;
        }
      } catch (error) {
        console.log(error);
        window.$nuxt.$swal.fire({
          title: "Mensaje!",
          text: "Error al procesar petición en el servidor",
          icon: "error",
          confirmButtonText: "Aceptar",
        });
      }
    },
    reset() {
      this.$refs.form.reset();
    },
    resetValidation() {
      this.$refs.form.resetValidation();
    },
  },
};
</script>
