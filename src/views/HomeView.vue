<script setup>
import ProductCard from "@/components/ProductCard.vue"; // komponen untuk menampilkan 1 produk
import PeginationPage from "@/components/PeginationPage.vue"; // komponen untuk pagination (prev/next)
import loading from "@/components/loading.vue"; // komponen loading
import { watch, ref, onMounted, watchEffect } from "vue"; // Vue API reaktif
import axios from "axios"; // untuk HTTP request
import ProductFrom from "@/components/ProductFrom.vue";

// state utama
const products = ref([]); // menampung daftar produk yang ditampilkan
const page = ref(1); // halaman aktif (mulai dari 1)
const limit = ref(8); // jumlah produk per halaman
const totalPages = ref(1); // total halaman
const isLoading = ref(true); // status loading

// fungsi untuk fetch data produk dari API
async function fetchData() {
  const start = (page.value - 1) * limit.value;
  const end = page.value * limit.value;

  const API_URL = `http://localhost:3000/products?_start=${start}&_end=${end}`;
  try {
    isLoading.value = true;
    const response = await axios.get(API_URL);
    products.value = response.data;

    // kalau x-total-count ada, pakai itu
    const totalItems = Number(response.headers["x-total-count"]);
    if (totalItems) {
      totalPages.value = Math.ceil(totalItems / limit.value);
    } else {
      // fallback: hitung manual total data
      const resAll = await axios.get("http://localhost:3000/products");
      totalPages.value = Math.ceil(resAll.data.length / limit.value);
    }
  } catch (error) {
    console.error("Error fetching data:", error);
  } finally {
    isLoading.value = false;
  }
}

watchEffect(() => {
  fetchData();
});

// fungsi untuk ganti halaman, dipanggil dari komponen pagination
function changePage(newPage) {
  if (newPage >= 1 && newPage <= totalPages.value) {
    page.value = newPage; // ini akan trigger watch()
  }
}

async function createdProduct(product) {
  try {
    await axios.post("http://localhost:3000/products", product);
    fetchData(); // refresh data
  } catch (error) {
    console.error("Error creating product:", error);
  } 
}
</script>

<template>
  <div v-if="isLoading">
    <loading />
  </div>
  <main v-else>
    <ProductFrom @created-Product="createdProduct" />
    <!-- grid untuk menampilkan produk -->
    <div class="product-grid">
      <!-- pakai ID produk agar render lebih stabil -->
      <ProductCard
        v-for="product in products"
        :key="product.id"
        :product="product"
      />
    </div>

    <!-- pagination -->
    <div class="pagination">
      <!-- event untuk ganti halaman -->
      <PeginationPage
        :page="page"
        :totalPages="totalPages"
        @change-page="changePage"
      />
    </div>
  </main>
</template>

<style scoped>
/* styling grid produk */
.product-grid {
  display: grid;
  grid-template-columns: repeat(
    auto-fill,
    minmax(250px, 1fr)
  ); /* responsive grid */
  gap: 20px;
  width: 80%;
  margin: 0 auto;
}

/* styling pagination */
.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 20px;
}
</style>
