<script setup>
import { ref, onMounted, watch } from 'vue';
import { db } from './data/guitarras';
import Guitarra from './components/Guitarra.vue';
import Header from './components/Header.vue';
import Footer from './components/Footer.vue';

const guitarras = ref([]);
const carrito = ref([]);
const guitarra = ref({});

const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value))
}

watch(carrito, () => {
    guardarLocalStorage();
}, {
    deep: true
})

onMounted(() => {
    guitarras.value = db;
    guitarra.value = db[3] // Modelo VAI

    const carritoStorage = localStorage.getItem('carrito');
    if(carritoStorage) {
        carrito.value = JSON.parse(carritoStorage);
    }
});

const agregarCarrito = (guitarra) => {
    const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id); // -1 si no encuentra la guitarra

    // SI la guitarra ya está en el carrito se aumenta la cantidad en 1
    if (existeCarrito >= 0) {
        carrito.value[existeCarrito].cantidad++;
    } else {
        guitarra.cantidad = 1;
        carrito.value.push(guitarra);
    }
}

const decrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id);
    // Si la cantidad es 1 o menos, no dejará decrementar más
    if (carrito.value[index].cantidad <= 1) return;
    carrito.value[index].cantidad--;
}

const incrementarCantidad = (id) => {
    const index = carrito.value.findIndex(producto => producto.id === id);
    carrito.value[index].cantidad++;
}

const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(guitarra => guitarra.id !== id);
}

const vaciarCarrito = () => {
    carrito.value = [];
}


</script>

<template>
    <Header 
        :carrito="carrito" 
        :guitarra="guitarra"
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
                :key="guitarra.id"
                :guitarra="guitarra" 
                @agregar-carrito="agregarCarrito" 
            />
        </div>
    </main>

    <Footer />
</template>
