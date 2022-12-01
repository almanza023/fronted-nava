<template>
  <v-simple-table>
    <template v-slot:default>
      <tbody>
       <tr><th>Cédula de Ciudadania</th>
       <td><v-btn
            fab
            small
            dark
            @click="store('CC', cedula)"
            color="indigo"
          >
            <v-icon dark>
              mdi-plus
            </v-icon>
          </v-btn></td>
       </tr>
       <tr>
        <td colspan="2">
        <v-file-input
          v-model="cedula"
          outlined
          dense
          required
        ></v-file-input>
        </td></tr>
        <tr><th>Certificado Afiliación de ARL</th>
        <td>
          <v-btn
            fab
            small
            dark
            @click="store('ARL', arl)"
            color="indigo"
          >
            <v-icon dark>
              mdi-plus
            </v-icon>
          </v-btn>
        </td>

        </tr>
       <tr><td colspan="2">
        <v-file-input
          v-model="arl"
          outlined
          dense
          required
        ></v-file-input>
        </td></tr>
        <tr><th>Certificado Afiliación de EPS</th>
        <td>
          <v-btn
            fab
            small
            dark
            @click="store('EPS', eps)"
            color="indigo"
          >
            <v-icon dark>
              mdi-plus
            </v-icon>
          </v-btn>
        </td>
        </tr>
       <tr><td colspan="2">
        <v-file-input
          v-model="eps"
          outlined
          dense
          required
        ></v-file-input>
        </td></tr>
         <tr><th>Certificado Afiliación de PENSION</th>
         <td>
          <v-btn
            fab
            small
            dark
            @click="store('PENSION', pension)"
            color="indigo"
          >
            <v-icon dark>
              mdi-plus
            </v-icon>
          </v-btn>
         </td>
         </tr>
       <tr><td colspan="2">
        <v-file-input
          v-model="pension"
          outlined
          dense
          required
        ></v-file-input>
        </td></tr>
      </tbody>
    </template>
  </v-simple-table>
</template>

<script>
export default {
  data: () => ({
    cedula:null,
    arl:null,
    eps:null,
    pension:null,
    valid: true,
  }),
  props:["solicitud"],

  methods: {

      redireccionar(){
        window.$nuxt.$router.push("/home");
      },
      async store(nombre, archivo) {

        if(archivo==null){
        window.$nuxt.$swal.fire({
          text: "Debe cargar un documento en "+nombre,
          icon: "warning",
          confirmButtonText: "Aceptar",
        });
        return;
        }

      let InstFormData = new FormData();
      InstFormData.append('solicitud_id' , this.solicitud);
      InstFormData.append('archivo' , archivo);
      InstFormData.append('nombre' , nombre);
      try {
       let respuesta= await  this.$axios.$post('documentos/cargar', InstFormData , {headers : {'content-type': 'multipart/form-data'}});
        if (respuesta.status == "success") {
          window.$nuxt.$swal.fire({
            text: respuesta.data,
            icon: respuesta.status,
            confirmButtonText: "Aceptar",
          });
        }else{
          window.$nuxt.$swal.fire({
          text: "Error al subir la información al servidor",
          icon: "error",
          confirmButtonText: "Aceptar",
        });
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
    },
}

</script>
