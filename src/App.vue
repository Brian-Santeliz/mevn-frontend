<template>
  <div id="app">
    <Navbar />
    <div v-if="edificaciones.length" class="contador">
      Total edificaciónes: {{ total }}
    </div>
    <section class="mtp">
      <mdb-container class="mt-4">
        <template v-if="edificaciones.length">
          <table class="table table-striped my-5">
            <thead class="text-white" style="background: rgb(55 174 193)">
              <tr>
                <th>#ID</th>
                <th>Nombre</th>
                <th>Ancho</th>
                <th>Alto</th>
                <th>Largo</th>
                <th>Material</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="edificacion in edificaciones" :key="edificacion.id">
                <th scope="row">{{ edificacion.id }}</th>
                <td>{{ edificacion.nombre }}</td>
                <td>{{ edificacion.ancho }} m</td>
                <td>{{ edificacion.alto }} m</td>
                <td>{{ edificacion.largo }} m</td>
                <td>{{ edificacion.material }}</td>
                <td>
                  <mdb-btn
                    class="m-0 ml-2 p-1 pb-2"
                    dark-waves
                    flat
                    icon-class="text-danger"
                    icon="trash"
                    size="sm"
                    title="Eliminar edificación"
                    @click="eliminarEdificacion(edificacion.id)"
                  />
                  <mdb-btn
                    class="m-0 ml-2 p-1 pb-2"
                    dark-waves
                    flat
                    icon-class="text-success"
                    icon="pen"
                    size="sm"
                    title="Editar edificación"
                    @click="
                      editar = true;
                      ejecutarModal = true;
                      datos = edificacion;
                    "
                  />
                </td>
              </tr>
            </tbody>
          </table>
        </template>
        <template v-else>
          <p class="text-center font-weight-bold text-capitalize mx-auto my-5">
            {{ mensaje }}
          </p>
        </template>
      </mdb-container>
    </section>
    <mdb-btn
      class="my-sm-0 px-3 btn-flotante"
      @click="ejecutarModal = true"
      icon="plus"
    >
      Agregar
    </mdb-btn>
    <Footer />
    <FormularioEdificaciones
      :mostrarModal="ejecutarModal"
      :editarDatos="editar"
      :datosEditar="datos && datos"
      :tituloFormulario="
        editar ? 'Editar edificación' : 'Agregar nueva edificación'
      "
      @cerrar="ejecutarModal = $event"
      @editar="editar = $event"
      @edificacion="crear($event)"
      @editarEdificacion="editarEdificacion($event)"
    />
  </div>
</template>

<script>
import Swal from "sweetalert2";
import { mdbContainer, mdbBtn } from "mdbvue";
import Navbar from "@/components/Navbar.vue";
import Footer from "@/components/Footer.vue";
import FormularioEdificaciones from "@/components/FormularioEdificaciones.vue";
export default {
  name: "App",
  components: {
    mdbContainer,
    mdbBtn,
    Navbar,
    Footer,
    FormularioEdificaciones,
  },
  data() {
    return {
      ejecutarModal: false,
      editar: false,
      edificaciones: [],
      datos: {},
      mensaje: "",
      url: "http://localhost:4000/api/edificaciones",
    };
  },
  async mounted() {
    this.obtenerEdificaciones();
  },
  computed: {
    total() {
      return this.edificaciones.length;
    },
  },
  methods: {
    async obtenerEdificaciones() {
      try {
        const response = await fetch(this.url);
        const data = await response.json();
        if (data?.data) {
          this.edificaciones = [];
          this.mensaje = data.msg;
        }
        this.edificaciones = data;
      } catch (error) {
        Swal.fire("error", `${error.message}`, "error");
      }
    },
    async crear(datos) {
      try {
        const response = await fetch(this.url, {
          method: "POST",
          headers: {
            "Content-type": "application/json",
          },
          body: JSON.stringify(datos),
        });
        const msg = await response.json();
        Swal.fire("Creado", `${msg}`, "success");
        this.obtenerEdificaciones();
      } catch (error) {
        Swal.fire("error", `${error.message}`, "error");
      }
    },
    async editarEdificacion(datos) {
      try {
        const response = await fetch(`${this.url}/${datos.id}`, {
          method: "PUT",
          headers: {
            "Content-type": "application/json",
          },
          body: JSON.stringify(datos),
        });
        const msg = await response.json();
        this.obtenerEdificaciones();
        Swal.fire("Actulizado", `${msg}`, "success");
      } catch (error) {
        Swal.fire("error", `${error.message}`, "error");
      }
    },
    async eliminarEdificacion(edificacionId) {
      try {
        Swal.fire({
          title: "¿Desea eliminar la edificación?",
          text: "Eliminar es una acción que no puede revertirse",
          icon: "warning",
          showCancelButton: true,
          cancelButtonText: "Cancelar",
          confirmButtonColor: "#3085d6",
          cancelButtonColor: "#d33",
          confirmButtonText: "Si, eliminar",
        }).then(async (result) => {
          if (result.isConfirmed) {
            const response = await fetch(`${this.url}/${edificacionId}`, {
              method: "DELETE",
            });
            const msg = await response.json();
            Swal.fire("Eliminado", `${msg}`, "success");
            this.edificaciones = this.edificaciones.filter(
              (edificacion) => edificacion.id !== edificacionId
            );
            if (!this.edificaciones.length) {
              this.mensaje = "No Hay Edificaciones Registradas";
            }
          }
        });
      } catch (error) {
        Swal.fire("error", `${error.message}`, "error");
      }
    },
  },
};
</script>
<style scoped>
body {
  margin: 0;
  width: 100%;
  background: #eee;
}
* {
  margin: 0;
  padding: 0;
}
.contador {
  position: absolute;
  right: 3em;
  bottom: 38em;
  font-weight: bold;
  background: #2bbbad;
  color: #fff;
  padding: 4px 9px;
  border-radius: 2em;
}
.mtp {
  margin-top: 3.5rem;
}
.btn-flotante {
  font-size: 16px;
  text-transform: uppercase;
  font-weight: bold;
  color: #ffffff;
  border-radius: 35px;
  letter-spacing: 2px;
  background-color: #1e8ee9;
  padding: 13px 15px;
  position: fixed;
  bottom: 70px;
  right: 40px;
  transition: all 300ms ease 0ms;
  box-shadow: 0px 8px 15px rgba(0, 0, 0, 0.1);
  z-index: 99;
}
.btn-flotante:hover {
  color: #fff;
  background-color: #0b70c3;
  box-shadow: 0px 15px 20px rgba(0, 0, 0, 0.3);
  transform: translateY(-7px);
}
@media only screen and (max-width: 600px) {
  .btn-flotante {
    font-size: 14px;
    padding: 12px 20px;
    bottom: 70px;
    right: 10px;
  }
}
</style>