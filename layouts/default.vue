<template>
  <v-app dark>
    <v-navigation-drawer v-model="drawer" :clipped="clipped" fixed app primary>
      <v-list>
        <v-list-item
          to="/home"
          router
          exact
        >
          <v-list-item-action >
            <v-icon>mdi-home</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Inicio</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
         <v-list-item
         v-if="perfil_id==3"
          to="/solicitudes"
          router
          exact
        >
          <v-list-item-action >
            <v-icon>mdi-chart-bubble</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Solicitudes</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item
         v-if="perfil_id==1"
          to="/solicitudes/crear"
          router
          exact
        >
          <v-list-item-action >
            <v-icon>mdi-chart-bubble</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Crear Solicitud</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
         <v-list-item
          v-if="perfil_id==2"
          to="/solicitudes/encargado"
          router
          exact
        >
          <v-list-item-action >
            <v-icon>mdi-chart-bubble</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Solicitudes Aprobadas</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
        <v-list-item
         v-if="perfil_id==3"
          to="/usuarios"
          router
          exact
        >
          <v-list-item-action >
            <v-icon>mdi-chart-bubble</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Usuarios</v-list-item-title>
          </v-list-item-content>
        </v-list-item>

        <v-list-item @click="logout">
          <v-list-item-action>
            <v-icon>mdi-logout-variant</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title>Cerrar Sesión</v-list-item-title>
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar :clipped-left="clipped" fixed app color="blue-grey lighten-4">
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-btn icon @click.stop="miniVariant = !miniVariant"> </v-btn>

      <v-toolbar-title>{{ title }}</v-toolbar-title>
      <v-spacer />
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt />
      </v-container>
    </v-main>
    <v-footer :absolute="!fixed" app>

      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      user_id: "",
      perfil_id: "",
      clipped: true,
      drawer: true,
      fixed: false,

      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "NAVA SISTEMA DE GESTION DOCUMENTAL",
    };
  },
  mounted() {
    this.user_id = localStorage.getItem("user_id");
    this.perfil_id = localStorage.getItem("perfil_id");
  },
  methods: {
    async logout() {
      await window.$nuxt.$swal
        .fire({
          title: "¿Esta seguro de cerrar la sesión?",
          showDenyButton: true,
          showCancelButton: false,
          confirmButtonText: "Aceptar",
          denyButtonText: `Cancelar`,
        })
        .then((result) => {
          /* Read more about isConfirmed, isDenied below */
          if (result.isConfirmed) {
            try {
              this.$http
                .$post("/usuarios/logout")
                .then(function (response) {
                  localStorage.clear();
                  window.$nuxt.$router.push("/");
                })
                .catch(function (error) {
                  console.log(error);
                });
            } catch (error) {
              console.log(error);
            }
          } else if (result.isDenied) {
            return;
          }
        });
    },
  },
};
</script>
