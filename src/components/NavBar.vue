<script setup>
import { RouterLink, useRouter } from 'vue-router';
import { inject } from 'vue';

const router = useRouter();
const isAuthenticated = inject("isAuthenticated");

const logout = () => {
  localStorage.removeItem("access_token");
  localStorage.removeItem("refresh_token");
  isAuthenticated.value = false;
  router.push("/login");
}

</script>

<template>
    <nav class="fixed top-0 left-0 w-full bg-white flex flex-row items-center justify-center gap-10 h-[50px] shadow-sm mb-10">
        <RouterLink to="/">Home</RouterLink>
        <RouterLink to="/organize/" v-if="isAuthenticated">Organize your day</RouterLink>
        <RouterLink to="/login/" v-if="!isAuthenticated">Login</RouterLink>
        <Button @click="logout" v-if="isAuthenticated">Logout</Button>
    </nav>
</template>
