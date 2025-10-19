<script setup>
// komponen untuk menampilkan detail produk
import { useRoute, useRouter } from "vue-router";
import { onMounted, ref } from "vue";
import axios from "axios";
import ProductFrom from "@/components/ProductFrom.vue";

const route = useRoute();
const router = useRouter();

const id = route.params.id; // ambil id dari parameter route
const product = ref({}); // state untuk menampung data produk
const API_URL = `http://localhost:3000/products/${id}`;

onMounted(() => {
  fetchProduct();
});
// fetch data produk berdasarkan id
async function fetchProduct() {
  try {
    const response = await axios.get(API_URL);
    product.value = response.data;
  } catch (error) {
    console.error("Error fetching product:", error);
  }
}
async function updatedProduct(updatedData) {
  try {
    await axios.patch(API_URL, updatedData);
    router.push("/");
  } catch (error) {
    console.error("Error updating product:", error);
    alert("Gagal update produk!");
  }
}

// fungsi untuk menghapus produk

async function deleteProduct() {
  try {
    await axios.delete(API_URL);
    router.push("/"); // ✔ balik ke home
  } catch (error) {
    console.error("Error deleting product:", error);
  }
}
</script>

<template>
  <div class="product-card">
    <h2 class="product-title">{{ product.title }}</h2>
    <img :src="product.image" :alt="product.title" class="product-image" />
    <p class="product-description">{{ product.description }}</p>
    <span class="product-price">Rp {{ product.price }}</span
    ><ProductFrom :product="product" @update-product="updatedProduct" />
    <!-- Edit button -->
    <div class="buttons">
      <router-link to="/" class="back-button">⬅ Back</router-link>
      <button class="back-button" @click="deleteProduct">Delete</button>
    </div>
  </div>
</template>

<style scoped>
.product-card {
  background-color: #fff;
  padding: 20px;
  border-radius: 12px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.08);
  transition: transform 0.25s ease-in-out, box-shadow 0.25s ease-in-out;
  cursor: pointer;
  max-width: 320px;
  margin: 20px auto;
  text-align: center;
}

.product-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 6px 14px rgba(0, 0, 0, 0.12);
}

.product-image {
  width: 100%;
  height: 200px;
  object-fit: cover;
  border-radius: 8px;
  margin-bottom: 15px;
}

.product-title {
  font-size: 20px;
  font-weight: bold;
  margin-bottom: 8px;
  color: #2d3436;
}

.product-description {
  color: #636e72;
  font-size: 14px;
  margin-bottom: 12px;
}

.product-price {
  display: inline-block;
  color: #00b894;
  font-size: 18px;
  font-weight: bold;
  margin: 15px;
}
.buttons {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 20px;
}

.back-button {
  margin: 15px;
  display: inline-block;
  padding: 8px 16px;
  background-color: #0984e3;
  color: #fff;
  border-radius: 6px;
  text-decoration: none;
  font-size: 14px;
  transition: background-color 0.3s ease;
}

.back-button:hover {
  background-color: #74b9ff;
}
</style>
