<script setup>
/*
  Import API dari Vue
  - defineProps  → untuk mendefinisikan props yang dikirim dari parent
  - defineEmits  → untuk mendefinisikan event yang bisa dikirim balik ke parent
*/
import { defineProps, defineEmits } from "vue";

/*
  Props dari parent:
  - page       → halaman saat ini
  - totalPages → total jumlah halaman
*/
const props = defineProps({
  page: Number,
  totalPages: Number,
});

/*
  Event yang akan dikirim balik ke parent
  - "change-page" → saat user klik tombol Prev/Next atau nomor halaman
*/
const emit = defineEmits(["change-page"]);

/*
  Fungsi ke halaman sebelumnya
  - hanya bisa dijalankan kalau page > 1
*/
function prevPage() {
  if (props.page > 1) emit("change-page", props.page - 1);
}

/*
  Fungsi ke halaman berikutnya
  - hanya bisa dijalankan kalau page < totalPages
*/
function nextPage() {
  if (props.page < props.totalPages) emit("change-page", props.page + 1);
}
</script>

<template>
  <div class="pagination">
    <!-- Tombol ke halaman sebelumnya -->
    <button @click="prevPage" :disabled="page === 1">Prev</button>
    page {{ page }} of {{ totalPages }}
    <!-- Tombol ke halaman berikutnya -->
    <button @click="nextPage" :disabled="page === totalPages">Next</button>
  </div>
</template>

<style scoped>
/* Styling sederhana */
.pagination {
  display: flex;
  align-items: center;
  gap: 8px;
}

button {
  padding: 6px 12px;
  border: 1px solid #ccc;
  background: white;
  cursor: pointer;
  border-radius: 4px;
}

button:disabled {
  background: #eee;
  cursor: not-allowed;
}

button.active {
  background: #007bff;
  color: white;
  font-weight: bold;
}
</style>
