<script setup>
import { RouterLink, RouterView } from "vue-router";
import { ref } from "vue";
import HomeView from "@/views/HomeView.vue";
</script>

<template>
  <!-- Router View akan ganti komponen sesuai route -->
  <RouterView />
</template>

<style scoped>
/* Style khusus untuk komponen ini */
</style>
