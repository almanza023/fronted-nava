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
              <v-text-field
                v-model="solicitud.nombres"
                :rules="regla"
                label="Nombres"
                outlined
                required
              ></v-text-field>
              <v-text-field
                v-model="solicitud.apellidos"
                :rules="regla"
                label="Apellidos"
                outlined
                required
              ></v-text-field>

              <v-text-field
                v-model="solicitud.num_doc"
                :rules="regla"
                label="Número Documento"
                max="10"
                type="number"
                outlined
                required
              ></v-text-field>

              <v-text-field
                v-model="solicitud.fecha_nac"
                :rules="regla"
                label="Fecha Nacimiento"
                @change="verifica"
                type="date"
                outlined
                required
              ></v-text-field>

              <v-text-field
                v-model="solicitud.telefono"
                :rules="regla"
                label="Teléfono"
                type="number"
                :counter="10"
                outlined
                required
              ></v-text-field>

              <v-text-field
                v-model="solicitud.correo"
                :rules="emailRules"
                label="Correo"
                outlined
                required
              ></v-text-field>

              <v-text-field
                v-model="solicitud.empresa"
                :rules="regla"
                label="Empresa"
                outlined
                required
              ></v-text-field>

               <v-text-field
                v-model="solicitud.orden_visita"
                :rules="regla"
                label="Orden Visita"
                outlined
                required
              ></v-text-field>

              <v-text-field
                v-model="solicitud.arl"
                :rules="regla"
                label="ARL"
                outlined
                required
              ></v-text-field>

              <v-text-field
                v-model="solicitud.eps"
                :rules="regla"
                label="EPS"
                outlined
                required
              ></v-text-field>

              <v-text-field
                v-model="solicitud.pension"
                :rules="regla"
                label="PENSION"
                outlined
                required
              ></v-text-field>
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
     <v-row justify="center" align="center" v-if="mostrar">
      <v-col cols="12" sm="8" md="10">
        <v-card class="elevation-12">
          <v-toolbar dark color="primary">
            <v-toolbar-title>CARGUE DE DOCUMENTOS</v-toolbar-title>
          </v-toolbar>
          <v-card-text>
            <v-form ref="form2" v-model="valid" lazy-validation>
              <CargueDocumentos :solicitud="solicitud.id" v-if="solicitud.id!=''"></CargueDocumentos>
            </v-form>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>
<script>
export default {
  data: () => ({
    valid: true,
    mostrar: false,
    solicitud: {
      id: "",
      nombres: "",
      apellidos: "",
      num_doc: "",
      fecha_nac: "",
      correo: "",
      telefono: "",
      empresa: "",
      orden_visita: "",
      arl: "",
      eps: "",
      pension: "",
    },
    regla: [(v) => !!v || "Campo es obligatorio"],
    email: "",
    emailRules: [
      (v) => !!v || "Correo es obligatorio",
      (v) => /.+@.+\..+/.test(v) || "Correo no es valido",
    ],
  }),

  mounted() {
    let id_usuario = localStorage.getItem("user_id");
    let perfil_id = localStorage.getItem("perfil_id");
    if ((id_usuario == null)) {
      window.$nuxt.$router.push("/");
    }
    if ((perfil_id != 1)) {
      window.$nuxt.$router.push("/home");
    }
  },

  methods: {
    validarFecha(fecha1) {
      let fecha2 = new Date()
        fecha2.toISOString().split('T')[0]
      if (fecha1 > fecha2) {
        return true;
      }
      return false;
    },

    async store() {
      if (!this.$refs.form.validate()) {
        return;
      }

      try {

      if (
        this.validarFecha(this.solicitud.fecha_nac)
      ) {
        window.$nuxt.$swal.fire({
          text: "La fecha nacimiento es mayor a la fecha actual",
          icon: "warning",
          confirmButtonText: "Aceptar",
        });
        return;
      }

        let datos = await this.$http.$post("solicitudes/crear", this.solicitud);
        if (datos.status == "success") {
          window.$nuxt.$swal.fire({
            text: datos.data,
            icon: datos.status,
            confirmButtonText: "Aceptar",
          });
          this.solicitud.id = await datos.solicitud_id;
          this.mostrar=true;
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
