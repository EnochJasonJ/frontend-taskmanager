<script setup>
import NavBar from './components/NavBar.vue';
import {RouterView } from 'vue-router';
import {ref, provide, onMounted, onUnmounted} from 'vue';
const isAuthenticated = ref(!!localStorage.getItem("access_token"));

const updateAuthStatus = () => {
  isAuthenticated.value = !!localStorage.getItem("access_token");
};

provide("isAuthenticated", isAuthenticated);
provide("updateAuthStatus", updateAuthStatus);

onMounted(() => {
  window.addEventListener("storage", updateAuthStatus);
})
onUnmounted(() => {
  window.removeEventListener("storage", updateAuthStatus);
})


</script>

<template>
  <NavBar />

  <div class="pt-16">
    <RouterView />
  </div>
</template>

