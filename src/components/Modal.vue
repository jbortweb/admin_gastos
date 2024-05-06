<script setup>
import { ref } from "vue";
import cerrarModal from "../assets/img/cerrar.svg";
import Alerta from "./Alerta.vue";

const error = ref("");

const emit = defineEmits([
  "cerrar-modal",
  "guardar-gasto",
  "eliminar-gasto",
  "update:nombre",
  "update:cantidad",
  "update:categoria",
]);

const props = defineProps({
  modal: {
    type: Object,
    required: true,
  },
  nombre: {
    type: String,
    required: true,
  },
  cantidad: {
    type: [String, Number],
    required: true,
  },
  categoria: {
    type: String,
    required: true,
  },
  disponible: {
    type: Number,
    required: true,
  },
  id: {
    type: [String, Number, null],
    required: true,
  },
});

const oldCantidad = props.cantidad;

const validarGasto = () => {
  const { nombre, cantidad, categoria, disponible, id } = props;

  //Validar campos vacios

  if ([nombre, cantidad, categoria].includes("")) {
    error.value = "Todos los campos son obligatorios";

    setTimeout(() => {
      error.value = "";
    }, 3000);
    return;
  }

  //Validar cantidad
  if (cantidad <= 0) {
    error.value = "La cantidad no es correcta";

    setTimeout(() => {
      error.value = "";
    }, 3000);
    return;
  }

  //Validar el presupuesto
  if (id) {
    if (cantidad > oldCantidad + disponible) {
      error.value = "Has superado el presupuesto";

      setTimeout(() => {
        error.value = "";
      }, 3000);
      return;
    }
  } else {
    if (disponible < cantidad) {
      error.value = "Has superado el presupuesto";

      setTimeout(() => {
        error.value = "";
      }, 3000);
      return;
    }
  }
  emit("guardar-gasto");
};
</script>

<template>
  <div class="modal">
    <div class="cerrar-modal">
      <img
        :src="cerrarModal"
        alt="Icono de cerrar modal"
        @click="$emit('cerrar-modal')"
      />
    </div>
    <div
      class="contenedor contenedor-formulario"
      :class="[modal.animar ? 'animar' : 'cerrar']"
    >
      <form @submit.prevent="validarGasto" class="nuevo-gasto">
        <legend>{{ id ? "Editar Gasto" : "Añadir Gasto" }}</legend>

        <Alerta v-if="error">{{ error }}</Alerta>

        <div class="campo">
          <label for="nombre">Nombre Gasto:</label>
          <input
            type="text"
            name="nombre"
            placeholder="Añade el Nombre del Gasto"
            :value="nombre"
            @input="$emit('update:nombre', $event.target.value)"
          />
        </div>
        <div class="campo">
          <label for="cantidad">Cantidad:</label>
          <input
            type="number"
            name="cantidad"
            placeholder="Añade la cantidad del Gasto, ej. 300"
            min="0"
            :value="cantidad"
            @input="$emit('update:cantidad', +$event.target.value)"
          />
        </div>
        <div class="campo">
          <label for="categoria">Categoria:</label>
          <select
            id="categoria"
            :value="categoria"
            @input="$emit('update:categoria', $event.target.value)"
          >
            <option value="">-- Seleccione --</option>
            <option value="ahorro">Ahorro</option>
            <option value="comida">Comida</option>
            <option value="casa">Casa</option>
            <option value="gastos">Gastos Varios</option>
            <option value="ocio">Ocio</option>
            <option value="salud">Salud</option>
            <option value="suscripciones">Suscripciones</option>
          </select>
        </div>
        <input type="submit" :value="[id ? 'Editar Gasto' : 'Añadir Gasto']" />
      </form>
      <button 
        type="button" 
        class="btn-eliminar"
        v-if="id"
        @click="$emit('eliminar-gasto', props.id)"
      >
        Eliminar Gasto
      </button>
    </div>
  </div>
</template>

<style scoped>
.modal {
  position: absolute;
  background-color: rgb(0 0 0 / 0.9);
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}
.cerrar-modal {
  position: absolute;
  right: 3rem;
  top: 3rem;
}
.cerrar-modal img {
  width: 3rem;
  cursor: pointer;
}

.contenedor-formulario {
  transition-property: all;
  transition-duration: 300ms;
  transition-timing-function: ease-in;
  opacity: 0;
}
.contenedor-formulario.animar {
  opacity: 1;
}

.contenedor-formulario.cerrar {
  opacity: 0;
}

.nuevo-gasto {
  margin: 5rem auto 0 auto;
  display: grid;
  gap: 2rem;
}
.nuevo-gasto legend {
  text-align: center;
  color: var(--blanco);
  font-size: 2.5rem;
  font-weight: 700;
}
.campo {
  display: grid;
  gap: 2rem;
}
.nuevo-gasto input,
.nuevo-gasto select {
  background-color: var(--gris-claro);
  border-radius: 1rem;
  padding: 1rem;
  border: none;
  font-size: 2rem;
}
.nuevo-gasto label {
  color: var(--blanco);
  font-size: 2.5rem;
}
.nuevo-gasto input[type="submit"] {
  background-color: var(--azul);
  color: var(--blanco);
  font-weight: 700;
  cursor: pointer;
}

.btn-eliminar {
  border: none;
  padding: 1rem;
  width: 100%;
  background-color: #ef4444;
  font-weight: 700;
  font-size: 1.5rem;
  color: var(--blanco);
  margin-top: 5rem;
  cursor: pointer;
  border-radius: 1rem;
}
</style>