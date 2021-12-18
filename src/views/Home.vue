<template>
  <div>
    <v-container class="grey lighten-5">
      <v-row>
        <v-col xs="12">
          <v-card>
            <v-date-picker
              v-model="fecha"
              full-width
              locale="es-cl"
              :min="minimo"
              :max="maximo"
              @change="getDolar(fecha)"
            ></v-date-picker>
          </v-card>
          <v-card color="error" dark class="pa-2" tile>
            <v-card-text class="text-h3 text-center">{{ valor }}</v-card-text>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>
import axios from "axios";
import { mapMutations } from "vuex";
export default {
  name: "Home",
  data() {
    return {
      fecha: new Date().toISOString().substr(0, 10),
      minimo: "1984",
      maximo: new Date().toISOString().substr(0, 10),
      valor: null,
    };
  },
  components: {},
  methods: {
    ...mapMutations(["mostrarLoading", "ocultarLoading"]),
    async getDolar(dia) {
      let arrayFecha = dia.split(["-"]);
      let ddmmyyyy = arrayFecha[2] + "-" + arrayFecha[1] + "-" + arrayFecha[0];
      try {
        this.mostrarLoading({ titulo: "CARGANDOOOOOO" });
        let datos = await axios.get(
          `https://mindicador.cl/api/dolar/${ddmmyyyy}`
        );
        console.log("HERE", datos.data);
        if (datos.data.serie.length > 0) {
          this.valor = await datos.data.serie[0].valor;
        } else {
          this.valor = "sin resultado";
        }
      } catch (e) {
        // console.log("La api no responde", e);
      } finally {
        this.ocultarLoading();
      }
    },
  },
  created() {
    this.getDolar(this.fecha);
  },
};
</script>
