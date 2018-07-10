<template>
  <div id="app">
    <login :sesion="sesion" v-if="!sesion" @sesion="checkSesion"></login>
    <main-component @logout="logout" v-if="sesion && sesion.role === 'Admin'"></main-component>
    <Prestamista @logout="logout" v-if="sesion && sesion.role === 'prestamista'" :sesion="sesion"></Prestamista>
  </div>
</template>

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
      this.sesion = null
    }
  },
  beforeMount () {
    this.checkSesion()
  }
}
</script>

<style>

</style>
