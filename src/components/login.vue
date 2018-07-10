<template>
<div class="container">
    <h1>Iniciar Sesión</h1>
    <form v-on:submit.prevent="login">
        <div class="form-group">
            <input type="text" class="form-control" id="user" placeholder="Usuario" v-model="user">
        </div>
        <div class="form-group">
            <input type="password" class="form-control" id="pass" placeholder="Contraseña" v-model="pass">
        </div>
        <button type="submit" class="btn btn-primary">Ingresar</button>
    </form>
</div>
</template>
<script>
export default {
  name: "login",
  data() {
    return {
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
            alert('Datos de inicio de sesión errados')
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


