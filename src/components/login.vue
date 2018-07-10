<template>
  <v-container grid-list-md text-xs-center>
    <v-layout row align-center justify-center>
      <v-flex xs12 sm7>
        <v-card class="elevation-5">
          <v-card-title class="header">
            <h1>Iniciar Sesión</h1>
          </v-card-title>
          <v-card-text>
            <v-form>
              <v-container>
                <v-text-field required label="Usuario" clearable v-model="user"></v-text-field>
                <v-text-field 
                required
                :append-icon="show ? 'visibility_off' : 'visibility'"
                @click:append="show = !show"
                label="Contraseña"
                v-model="pass" :type="show ? 'text' : 'password'"></v-text-field>
                <v-btn color="info" block @click="login">Ingresar</v-btn>
              </v-container>
            </v-form>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<style>
  .header {
    background-color: #2196f3;
  }
  .header h1 {
    margin: 0 !important;
  }
</style>

<script>
import swal from 'sweetalert2'
export default {
  name: "login",
  data() {
    return {
      show: false,
      user: "",
      pass: ""
    };
  },
  props: ['sesion'],
  methods: {
    login: function () {
        if (this.user === 'Admin' && this.pass === 'Admin') {
            window.localStorage.setItem('sesion', JSON.stringify({
                user: 'Admin',
                pass: 'Admin',
                role: 'Admin'
            }))
            this.$emit('sesion')
        } else {
            const prestamistas = JSON.parse(localStorage.getItem('pres')) || []
            for (let prestamista of prestamistas) {
                if (this.user === prestamista.name && this.pass === prestamista.password) {
                    window.localStorage.setItem('sesion',JSON.stringify(prestamista))
                    return this.$emit('sesion')
                }
            }
            swal("Ooops...", "Datos de inicio de sesión errados", "error");
            this.pass = ''
        }
    }
  }
};
</script>
<style scoped>
    h1{
        margin-top: 40px;
        text-align: center;
        color: rgba(0, 0, 0, .5);
        font-size: 35px;
    }
</style>


