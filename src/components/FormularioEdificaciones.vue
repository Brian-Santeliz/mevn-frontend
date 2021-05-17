<template>
  <mdb-modal :show="mostrarModal" @close="cerrarModal">
    <mdb-modal-header>
      <mdb-modal-title>{{ tituloFormulario }}</mdb-modal-title>
    </mdb-modal-header>
    <mdb-modal-body>
      <form @submit.prevent="submitFormulario">
        <label for="select" class="field-label">Edificación:</label>
        <select
          id="select"
          class="custom-select form-control"
          v-model="edificacion.nombre"
        >
          <option disabled value="">Seleccione el tipo de edificación</option>
          <option
            v-for="(value, key) in edificaciones"
            :key="`edificacion-${key}`"
            :value="value"
          >
            {{ value }}
          </option>
        </select>
        <mdb-input
          v-model="edificacion.alto"
          label="Indique el alto"
          icon="text-height"
          type="number"
          class="mb-5"
          outline
        />
        <mdb-input
          v-model="edificacion.ancho"
          label="Indique el ancho"
          icon="text-width"
          type="number"
          class="mb-5"
          outline
        />
        <mdb-input
          v-model="edificacion.largo"
          type="number"
          label="Indique el largo"
          icon="expand-arrows-alt"
          class="mb-5"
          outline
        />
        <mdb-input
          v-model.trim="edificacion.material"
          label="Indique el material"
          type="text"
          icon="hammer"
          class="mb-5"
          outline
        />
        <mdb-btn
          type="submit"
          :color="editarDatos ? 'success' : 'primary'"
          class="text-center mx-auto d-flex justify-content-start"
          :icon="editarDatos ? 'building' : 'paper-plane'"
          >{{ botonTexto }}</mdb-btn
        >
      </form>
    </mdb-modal-body>
  </mdb-modal>
</template>

<script>
import Swal from "sweetalert2";
import {
  mdbModal,
  mdbModalHeader,
  mdbModalTitle,
  mdbModalBody,
  mdbModalFooter,
  mdbBtn,
  mdbInput,
} from "mdbvue";
export default {
  name: "FormularioEdificaciones",
  props: {
    mostrarModal: {
      type: Boolean,
      required: true,
    },
    editarDatos: {
      type: Boolean,
      required: false,
    },
    tituloFormulario: {
      type: String,
      required: false,
    },
    datosEditar: {
      type: Object,
      required: false,
      default: function () {
        return () => {};
      },
    },
  },
  components: {
    mdbModal,
    mdbModalHeader,
    mdbModalTitle,
    mdbModalBody,
    mdbModalFooter,
    mdbBtn,
    mdbInput,
  },
  data() {
    return {
      edificacion: {
        nombre: "",
        alto: "",
        largo: "",
        ancho: "",
        material: "",
      },
      edificaciones: [
        "Edificio",
        "Casa",
        "Apartamento",
        "Carretera",
        "Parque",
        "Puente",
        "Zonas comerciales",
        "Otro",
      ],
    };
  },
  watch: {
    editarDatos() {
      this.editarDatos && Object.assign(this.edificacion, this.datosEditar);
    },
  },
  computed: {
    botonTexto() {
      return this.editarDatos ? "Editar edificación" : "Crear edificación";
    },
  },
  methods: {
    submitFormulario() {
      if (
        !this.edificacion.nombre.length ||
        !this.edificacion.alto ||
        !this.edificacion.largo ||
        !this.edificacion.ancho ||
        !this.edificacion.material
      ) {
        return Swal.fire("Error", "Todos los campos son obligatorios", "error");
      }
      if (this.editarDatos) {
        this.$emit("editarEdificacion", this.edificacion);
        this.cerrarModal();
        return;
      }
      this.$emit("edificacion", this.edificacion);
      this.cerrarModal();
    },
    cerrarModal() {
      this.$emit("cerrar", false);
      this.$emit("editar", false);
      this.edificacion = {
        nombre: "",
        alto: "",
        largo: "",
        ancho: "",
        material: "",
      };
    },
  },
};
</script>
