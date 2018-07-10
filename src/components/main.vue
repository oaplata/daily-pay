<template>
    <div>
        <button class="btn btn-link" @click="logout()"> Cerrar Sesion</button>
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Registrar Prestamistas</h5>
                <form @submit.prevent="registrar">
                    <div class="form-group">
                        <label for="nombre">Nombre*</label>
                        <input type="text" id="nombre" placeholder="nombre" v-model="nombrePrestamista" class="form-control">
                    </div>

                    <div class="form-group">
                        <label for="pass">Contraseña*</label>
                        <input type="password" id="pass" placeholder="Contraseña" v-model="passPrestamista" class="form-control">
                    </div>

                    <div class="form-group">
                        <label for="money">Dinero</label>
                        <input type="text" id="money" placeholder="Dinero" v-model="money" class="form-control">
                    </div>
                    <button type="submit" class="btn btn-primary">Registrar</button>
                </form>
            </div>
        </div>
        <div class="card">
            <div class="card-body">
                <h5 class="card-title">Prestamistas</h5>
                <button class="btn btn-primary" @click="mostrar()">Mostrar prestamos</button>
                <ul class="list-group">
                    <li class="list-group-item" v-for="(prestamista, index) in prestamistas" :key="prestamista.name">
                        <p>{{prestamista.name}} - ${{formatPrice(prestamista.money)}}</p>
                        <button type="button" class="btn btn-danger" @click="eliminar(index)">Eliminar</button>
                        <button class="btn btn-success" @click="prestar(index)">Prestar</button>

                        <div v-if="show">
                            <div class="card" v-for="(prestamo, index) in prestamista.prestamos" :key="index">
                                <div class="card-body">
                                    <h4>{{prestamo.from}}</h4>
                                    <p><b>Direccion: </b> {{prestamo.dir}}</p>
                                    <p><b>Telefono: </b> {{prestamo.tel}}</p>
                                    <p><b>Meses: </b> {{prestamo.time}}</p>
                                    <p><b> Cantidad prestada: </b>  ${{formatPrice(prestamo.money)}}</p>
                                    <p><b>Cantidad pagada:</b> ${{formatPrice(prestamo.pagada)}} </p>
                                    <p><b>Cantidad Faltante:</b> ${{formatPrice(prestamo.left)}} </p>
                                </div>
                            </div>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
</template>

<script>
export default {
  name: "main-component",
  data() {
    return {
      nombrePrestamista: null,
      passPrestamista: null,
      money: null,
      prestamistas: [],
      show: false
    };
  },
  methods: {
    registrar() {
      console.log("prestamo");
      if (!this.passPrestamista || !this.nombrePrestamista) {
        return alert("Completa por favor los campos requeridos");
      }
      const prestamista = {
        name: this.nombrePrestamista,
        password: this.passPrestamista,
        money: this.money,
        role: "prestamista"
      };

      this.prestamistas.push(prestamista);
      localStorage.setItem("pres", JSON.stringify(this.prestamistas));
    },
    formatPrice(value) {
      let val = (value / 1).toFixed(0).replace(".", ",");
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
    },
    eliminar(prestamista) {
        this.prestamistas.splice(prestamista, 1)
        localStorage.setItem('pres', JSON.stringify(this.prestamistas))
    },
    prestar(index) {
        const money = window.prompt('Ingrese el valor que se le va a entregar al prestamista')
        const prestamista = this.prestamistas[index]
        prestamista.money = parseInt(money) + parseInt(prestamista.money)
        this.prestamistas[index] = prestamista

        localStorage.setItem('pres', JSON.stringify(this.prestamistas))
    },
    logout() {
        window.localStorage.removeItem('sesion')
        this.$emit('logout')
    },
    mostrar() {
        this.show = !this.show
    }


  },
  beforeMount() {
    this.prestamistas = JSON.parse(localStorage.getItem("pres")) || [];
  }
};
</script>

<style>
.card {
  width: 98%;
  margin-left: 1%;
  margin-top: 10px;
}
</style>
