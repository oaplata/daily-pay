<template>
  <div class="contenedor">
    <v-layout wrap align-center justify-center>
      <v-flex xs12 md7 class="cont">
        <v-card>
          <v-card-title class="header">
            <h1 class="card-title">Crear Prestamo</h1>
          </v-card-title>
          <v-card-text class="card-content">
            <v-form @submit.prevent="registrar">
              <v-text-field required label="Nombre" clearable v-model="from"></v-text-field>
              <v-text-field required label="Dirección" clearable v-model="dir"></v-text-field>
              <v-text-field required label="Teléfono" clearable v-model="tel"></v-text-field>
              <v-text-field required mask="##" label="Meses" clearable v-model="time"></v-text-field>
              <v-text-field
              mask="##############"
              prepend-icon="attach_money" required label="Cantidad" clearable v-model="money" @keyup.enter="create"></v-text-field>
              <v-btn color="primary" @click="create">Crear Prestamo</v-btn>
            </v-form>
          </v-card-text>
        </v-card>
      </v-flex>
      <v-flex xs12 md5 class="cont">
        <v-card>
          <v-card-title class="header">
            <h1 class="card-title">Prestamos</h1>
          </v-card-title>
          <v-card-text class="card-content scroll-overflow">
            <v-list>
              <v-list-tile
                v-for="(prestamista, index) in user.prestamos"
                :key="prestamista.from"
                @click="openDialog(prestamista)"
              >
                <v-list-tile-content>
                  <v-list-tile-title v-text="prestamista.from"></v-list-tile-title>
                  <v-list-tile-sub-title v-html="`$ ${formatPrice(prestamista.left)}`"></v-list-tile-sub-title>
                </v-list-tile-content>
                <v-list-tile-action>
                  <v-tooltip top>
                    <v-btn slot="activator" icon ripple @click.stop="add(index)">
                      <v-icon color="orange lighten-1">payment</v-icon>
                    </v-btn>
                  <span>Añadir Cuota</span>
                  </v-tooltip>
                </v-list-tile-action>
                <v-list-tile-action>
                  <v-tooltip top>
                    <v-btn slot="activator" icon ripple @click.stop="openDialog(prestamista)">
                      <v-icon color="grey lighten-1">info</v-icon>
                    </v-btn>
                    <span>Información</span>
                  </v-tooltip>
                </v-list-tile-action>
              </v-list-tile>
            </v-list>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
    <v-dialog
      v-model="dialog"
      max-width="500"
    >
      <v-card>
        <v-card-title class="header no-bg">
          <h3>{{selectedPrestamo.from}}</h3>
          <h3>$ {{formatPrice(selectedPrestamo.left)}}</h3>
        </v-card-title>

        <v-card-text>
          <v-subheader slot="activator">Información del cliente</v-subheader>
          <v-list>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">location_on</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Direccion'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="selectedPrestamo.dir"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">phone</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Teléfono'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="selectedPrestamo.tel"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">calendar_today</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Meses'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="selectedPrestamo.time"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">attach_money</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Cantidad Prestada'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="formatPrice(selectedPrestamo.money)"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">attach_money</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Cantidad Pagada'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="formatPrice(selectedPrestamo.pagada)"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">attach_money</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Cantidad Faltante'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="formatPrice(selectedPrestamo.left)"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list>
        </v-card-text>

        <v-card-actions>
          <v-btn
            color="red darken-1"
            flat="flat"
            @click="dialog = false"
          >
            Cerrar
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
import swal from 'sweetalert2'
export default {
  name: "prestamista",
  props: ["sesion"],
  data() {
    return {
      dialog: false,
      selectedPrestamo: {},
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
    openDialog(prestamo) {
      this.dialog = true
      this.selectedPrestamo = prestamo
    },
    async add(index) {
      let {value: cuota} = await swal({
        title: 'Ingrese el valor de la cuota',
        input: 'number',
        inputPlaceholder: 'Cuota',
        showCancelButton: true,
        inputValidator: (value) => {
          return !value && 'Debes ingresar un valor'
        }
      })
      cuota = cuota || 0
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
      this.$emit('setSesion')
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
