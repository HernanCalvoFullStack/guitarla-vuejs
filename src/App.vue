<!-- colocanto setup adentro del script estamos habilitando Composition API -->
<script setup>
  import { ref, onMounted, watch } from "vue"
  import { db } from "./data/guitarras" 
  import Guitarra from "./components/Guitarra.vue"
  import Header from "./components/Header.vue";
  import Footer from "./components/Footer.vue";

/* Generado con Reactive
  const state = reactive({
    guitarras: db
  })
  console.log(state.guitarras) */

// Generado con Ref
  const guitarras = ref([])
  const carrito = ref([])
  const guitarra = ref({})

  // watch revisa que state esté cambiando
  watch(carrito, () => {
    guardarLocalStorage()
  }, {
    deep: true
  })

  // onMounted carga cuando la página está lista
  onMounted(() => {
    guitarras.value = db
    guitarra.value = db[3]

    const carritoStorage = localStorage.getItem("carrito")
    if(carritoStorage) {
        carrito.value = JSON.parse(carritoStorage)
    }
  })

  const guardarLocalStorage = () => {
    localStorage.setItem("carrito", JSON.stringify(carrito.value))
  }

  const agregarCarrito = (guitarra) => {
    // Comprobamos si existe el producto en el carrito, si existe aumentamos la cantidad
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
    if(existeCarrito >= 0) {
        carrito.value[existeCarrito].cantidad++
    } else {
        guitarra.cantidad = 1
        carrito.value.push(guitarra)
    }

    guardarLocalStorage()
  }

  const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad <= 1) return
    carrito.value[index].cantidad--
  }

  const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id)
    if(carrito.value[index].cantidad >= 5) return
    carrito.value[index].cantidad++
  }

  const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter( producto => producto.id !== id)
  }

  const vaciarCarrito = () => {
    carrito.value = []
  }

</script>

<template>

    <Header 
        v-bind:carrito="carrito"
        v-bind:guitarra="guitarra"
        @incrementar-cantidad="incrementarCantidad"
        @decrementar-cantidad="decrementarCantidad"
        @agregar-carrito="agregarCarrito"
        @eliminar-producto="eliminarProducto"
        @vaciar-carrito="vaciarCarrito"
    />

    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra 
              v-for="guitarra in guitarras"
              v-bind:guitarra="guitarra"
              @agregar-carrito="agregarCarrito"
            />
        </div>
    </main>

    <Footer />

</template>

<style scoped>

</style>
