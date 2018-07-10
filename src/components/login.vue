<template>
  <v-layout row align-center justify-center>
    <v-flex sm6>
      <v-layout style="background-color: #2980b9; height: 100vh" wrap row align-center justify-center>
          <v-flex xs12>
            <h1>App Prestamos</h1>
          </v-flex>
          <img class="imagen" src="http://pngimg.com/uploads/coin/coin_PNG36871.png" width="200" />
      </v-layout>
    </v-flex>
    <v-flex xs12 sm6>
      <div class="login-cont">
        <h1>Iniciar Sesión</h1>
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
      </div>
    </v-flex>
  </v-layout>
</template>

<style>
  .login-cont {
    padding: 0 30px;
  }
  .imagen {
    padding: 40px 0;
  }
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


