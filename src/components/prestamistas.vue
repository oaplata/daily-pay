<template>
    <div class="container">
        <h1>Prestamos</h1>
        <ul class="list-group">
            <li class="list-group-item" v-for="(prestamo, index) in user.prestamos" :key="prestamo.from">
                <div class="card">
                    <div class="card-body">
                        <h4>{{prestamo.from}}</h4>
                        <p><b>Direccion: </b> {{prestamo.dir}}</p>
                        <p><b>Telefono: </b> {{prestamo.tel}}</p>
                        <p><b>Meses: </b> {{prestamo.time}}</p>
                        <p><b> Cantidad prestada: </b>  ${{formatPrice(prestamo.money)}}</p>
                        <p><b>Cantidad pagada:</b> ${{formatPrice(prestamo.pagada)}} </p>
                        <p><b>Cantidad Faltante:</b> ${{formatPrice(prestamo.left)}} </p>
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
                <label for="dir">Direccion</label>
                <input type="text" id="dir" placeholder="Direccion" class="form-control" v-model="dir">
            </div>
            <div class="form-group">
                <label for="tel">Telefono</label>
                <input type="text" id="tel" placeholder="Telefono" class="form-control" v-model="tel">
            </div>
            <div class="form-group">
                <label for="money">Cantidad</label>
                <input type="number" name="money" id="money" placeholder="Cantidad" class="form-control" v-model="money">
            </div>
            <div class="form-group">
                <label for="time">Meses</label>
                <input type="number" name="time" id="time" placeholder="Meses" class="form-control" v-model="time">
            </div>
            <button type="submit" class="btn btn-warning">Crear Prestamo</button>
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
      user: null,
      dir: null,
      time: null,
      tel: null
    }
  },
  beforeMount() {
    const user = JSON.parse(localStorage.getItem('pres'))
    this.user = user.filter(p => p.name === this.sesion.name)[0]
    console.log(this.user)
  },
  methods: {
    add(index) {
      const cuota = prompt("Ingrese el valor de la cuota")
      this.user.prestamos[index].pagada += parseInt(cuota)
      this.user.prestamos[index].left -= parseInt(cuota)
      this.user.money = parseInt(cuota) + parseInt(this.user.money)
      this.guardarSesion()
    },
    create() {
      const prestamo = {
        dir: this.dir,
        time: this.time,
        tel: this.tel,
        from: this.from,
        money: this.money,
        pagada: 0,
        left: ( parseInt(this.money) + parseInt( (this.time * 0.1) * this.money ) )
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
    },
    formatPrice(value) {
      let val = (value / 1).toFixed(0).replace(".", ",");
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
    }
  }
};
</script>

<style>
</style>
