<template>
  <div class="contenedor">
    <v-layout wrap>
      <v-flex xs12 sm7 class="cont">
        <v-card>
          <v-card-title class="header">
            <h1 class="card-title">Registrar Prestamistas</h1>
          </v-card-title>
          <v-card-text>
            <v-form @submit.prevent="registrar">
              <v-text-field required label="Nombre" clearable v-model="nombrePrestamista"></v-text-field>
              <v-text-field 
              required
              :append-icon="showPass ? 'visibility_off' : 'visibility'"
              @click:append="showPass = !showPass"
              label="Contraseña"
              v-model="passPrestamista"
              :type="showPass ? 'text' : 'password'"></v-text-field>
              <v-text-field
              mask="################"
              prepend-icon="attach_money" required label="Dinero" clearable v-model="money" @keyup.enter="registrar"></v-text-field>
              <v-btn color="primary" @click="registrar">Registrar</v-btn>
            </v-form>
          </v-card-text>
        </v-card>
      </v-flex>
      <v-flex xs12 sm5 class="cont">
        <v-card>
          <v-card-title class="header">
            <h1 class="card-title">Prestamistas</h1>
          </v-card-title>
          <v-card-text>
            <v-list>
              <v-list-tile
                v-for="(prestamista, index) in prestamistas"
                :key="prestamista.name"
                @click="openDialog(prestamista)"
              >
                <v-list-tile-content>
                  <v-list-tile-title v-text="prestamista.name"></v-list-tile-title>
                  <v-list-tile-sub-title v-html="`$ ${formatPrice(prestamista.money)}`"></v-list-tile-sub-title>
                </v-list-tile-content>
                <v-list-tile-action>
                  <v-btn icon ripple @click="prestar(index)">
                    <v-icon color="orange lighten-1">payment</v-icon>
                  </v-btn>
                </v-list-tile-action>
                <v-list-tile-action>
                  <v-btn icon ripple @click="eliminar(index)">
                    <v-icon color="red lighten-1">delete</v-icon>
                  </v-btn>
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
          <h3>{{selectedPrestamista.name}}</h3>
          <h3>$ {{formatPrice(selectedPrestamista.money)}}</h3>
        </v-card-title>

        <v-card-text>
          <v-list subheader>
            <v-list-group
            v-for="cliente in selectedPrestamista.prestamos"
            v-model="cliente.active"
            :key="cliente.tel"
            no-action
          >
            <v-subheader inset>Prestamos</v-subheader>
            <v-list-tile slot="activator">
              <v-list-tile-content>
                <v-list-tile-title>{{ cliente.from }}</v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>

            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">location_on</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Direccion'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="cliente.dir"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">phone</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Teléfono'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="cliente.tel"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">calendar_today</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Meses'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="cliente.time"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">attach_money</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Cantidad Prestada'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="formatPrice(cliente.money)"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">attach_money</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Cantidad Pagada'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="formatPrice(cliente.pagada)"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile @click="() => false">
              <v-list-tile-action>
                <v-icon color="indigo">attach_money</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title v-text="'Cantidad Faltante'"></v-list-tile-title>
                <v-list-tile-sub-title v-html="formatPrice(cliente.left)"></v-list-tile-sub-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list-group>
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
<style>
  .contenedor {
    padding: 20px
  }
  .cont:first-child {
    padding-right: 10px;
  }
  .cont:last-child {
    padding-left: 10px;
  }
  .header {
    display: flex;
    justify-content: space-between;
    background-color: #2196f3;
  }
  .header h1 {
    color: rgba(0, 0, 0, .5);
    margin: 0 !important;
  }
  .no-bg {
    background-color: rgba(0, 0, 0, .1)
  }
</style>

<script>
import swal from 'sweetalert2'
export default {
  name: "main-component",
  data() {
    return {
      dialog: false,
      items: [{
        title: 'Nicolas',
        active: false
      }],
      selectedPrestamista: {
        money: 0,
        prestamos: []
      },
      showPass: false,
      nombrePrestamista: null,
      passPrestamista: null,
      money: null,
      prestamistas: [],
      show: false
    };
  },
  methods: {
    openDialog (prestamista) {
      prestamista.prestamos = prestamista.prestamos.map(e => {
        e.active = false
        return e
      })
      this.dialog = true
      this.selectedPrestamista = prestamista
      console.log(this.selectedPrestamista)
    },
    registrar() {
      console.log("prestamo");
      if (!this.passPrestamista || !this.nombrePrestamista) {
        return swal("Ooops...", "Completa por favor los campos requeridos", "error");
      }
      const prestamista = {
        name: this.nombrePrestamista,
        password: this.passPrestamista,
        money: this.money == null ? 0 : this.money,
        role: "prestamista"
      };
      this.prestamistas.push(prestamista);
      localStorage.setItem("pres", JSON.stringify(this.prestamistas));
      this.nombrePrestamista = null
      this.passPrestamista = null
      this.money = 0
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
        let money = window.prompt('Ingrese el valor que se le va a entregar al prestamista')
        money = money === null ? 0 : money
        console.log(money)
        const prestamista = this.prestamistas[index]
        console.log(prestamista.money)
        prestamista.money = parseInt(money) + parseInt(prestamista.money)
        this.prestamistas[index] = prestamista

        localStorage.setItem('pres', JSON.stringify(this.prestamistas))
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
