<template>
  <div id="app">
    <v-app>
      <v-toolbar dark v-if="sesion" color="primary">
        <v-toolbar-title>
          <div class="prestamista-title">
            <h4>{{sesion.user || sesion.name}}</h4>
            <span v-if="sesion.role == 'prestamista'">Saldo del prestamista: ${{formatPrice(sesion.money)}}</span>
          </div>
        </v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-items>
          <v-btn flat @click="logout">Cerrar Sesión</v-btn>
        </v-toolbar-items>
      </v-toolbar>
      <login :sesion="sesion" v-if="!sesion" @sesion="checkSesion"></login>
      <main-component @logout="logout" v-if="sesion && sesion.role === 'Admin'"></main-component>
      <Prestamista @setSesion="checkSesion" @logout="logout" v-if="sesion && sesion.role === 'prestamista'" :sesion="sesion"></Prestamista>
    </v-app>
  </div>
</template>
<style scoped>
.prestamista-title {
  display: flex;
  flex-direction: column;
  justify-content: center;
}
  .prestamista-title span {
    font-size: 15px;
  }
  
</style>

<script>
import Login from './components/login.vue'
import MainComponent from './components/main.vue'
import Prestamista from './components/prestamistas.vue'
export default {
  name: 'app',
  components: {
    Login,
    MainComponent,
    Prestamista
  },
  data () {
    return {
      sesion: null
    }
  },
  methods: {

    checkSesion: function () {
      const storage = window.localStorage.getItem('sesion')
      if ( storage ) {
        this.sesion = JSON.parse(storage)
      }
    },
    logout() {
      window.localStorage.removeItem('sesion')
      this.sesion = null
    },
    formatPrice(value) {
      let val = (value / 1).toFixed(0).replace(".", ",");
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
    }
  },
  beforeMount () {
    this.checkSesion()
  }
}
</script>

<style>

</style>
