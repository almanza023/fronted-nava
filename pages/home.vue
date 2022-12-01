<template>
  <div>
    <v-row>
      <v-col cols="12">
        <v-card>
          <v-jumbotron>
            <v-container fill-height>
              <v-layout align-center>
                <v-flex>
                  <h3 class="display-3">{{ nombre_user }}</h3>
                  <center>
                    <v-img src="/logo.png" max-height="350"
                                          max-width="450"></v-img>
                  </center>
                 <vue-clock class="font-size: 14px;"/>
                </v-flex>
              </v-layout>
            </v-container>
          </v-jumbotron>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>
<script>
export default {
  data() {
    return {
      loading: false,
      nombre_user: ""
    };
  },
  mounted() {
    let id_usuario = localStorage.getItem("user_id");
    this.nombre_user = localStorage.getItem("nombre");
    if (id_usuario == null) {
      window.$nuxt.$router.push("/");
    }
  },

  methods: {},
};
</script>
