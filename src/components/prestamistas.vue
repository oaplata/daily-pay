<template>
    <div class="container">
        <button class="btn btn-link" @click="logout()">Cerrar Sesion</button>
        <h4>Saldo del prestamista: {{user.money}}</h4>
        <h1>Prestamos</h1>
        <ul class="list-group">
            <li class="list-group-item" v-for="(prestamo, index) in user.prestamos" :key="prestamo.from">
                <div class="card">
                    <div class="card-body">
                        <h4>{{prestamo.from}}</h4>
                        <p><b> Cantidad prestada: </b>  {{prestamo.money}}</p>
                        <p><b>Cantidad pagada:</b> {{prestamo.pagada}} </p>
                        <p><b>Cantidad Faltante:</b> {{prestamo.left}} </p>
                        <button class="btn btn-primary" @click="add(index)">Añadir cuota</button>
                    </div>
                </div>
            </li>    
        </ul>   
        <h1>Crear Prestamo</h1>
        <form @submit.prevent="create()">
            <div class="form-group">
                <label for="name">Nombre</label>
                <input type="text" id="name" placeholder="Nombre" class="form-control" v-model="from">
            </div>
            <div class="form-group">
                <label for="money">Cantidad</label>
                <input type="number" name="money" id="money" placeholder="Cantidad" class="form-control" v-model="money">
            </div>
            <button type="submit" class="btn btn-warning">Crear Preestamo</button>
        </form>
    </div>    
</template>

<script>
export default {
  name: "prestamista",
  props: ["sesion"],
  data() {
    return {
      from: "",
      money: 0,
      user: null
    }
  },
  beforeMount() {
    const user = JSON.parse(localStorage.getItem('pres'))
    this.user = user.filter(p => p.name === this.sesion.name)[0]
    console.log(this.user)
  },
  methods: {
    logout() {
      window.localStorage.removeItem('sesion')
      this.$emit('logout')
    },
    add(index) {
      const cuota = prompt("Ingrese el valor de la cuota")
      this.user.prestamos[index].pagada += parseInt(cuota)
      this.user.prestamos[index].left -= parseInt(cuota)
      this.user.money = parseInt(cuota) + parseInt(this.user.money)
      this.guardarSesion()
    },
    create() {
      const prestamo = {
        from: this.from,
        money: this.money,
        pagada: 0,
        left: this.money
      }
      if (!this.user.prestamos) this.user.prestamos = []
      this.user.prestamos.push(prestamo)
      this.user.money -= parseInt(this.money)
      this.guardarSesion()
    },
    guardarSesion() {
      let prestamistas = JSON.parse(localStorage.getItem("pres"))
      prestamistas = prestamistas.map(p => {
        if (p.name === this.user.name) return this.user
        return p
      })

      localStorage.setItem("pres", JSON.stringify(prestamistas))
      localStorage.setItem('sesion', JSON.stringify(this.user))
    }
  }
};
</script>

<style>
</style>
