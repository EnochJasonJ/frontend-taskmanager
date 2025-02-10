<script setup>
import { ref, inject } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';

const router = useRouter();
const username = ref('');
const password = ref('');
const errormessage = ref('');
const updateAuthStatus = inject("updateAuthStatus");
const login = async () => {
  try {
    const response = await axios.post("http://127.0.0.1:8000/token/", {
      username : username.value,
      password : password.value
    });
    localStorage.setItem("access_token",response.data.access);
    localStorage.setItem("refresh_token",response.data.refresh);
    updateAuthStatus();
    router.push("/organize/");
  } catch (error) {
    errormessage.value = "Invalid username or password" + error;
  }
}
</script>

<template>
  <div class="flex flex-col items-center justify-center">
    <h2>Sign In</h2>
    <div class="flex flex-col items-center justify-center">
      <form @submit.prevent="login" class="flex w-[400px] mt-20 rounded-lg flex-col items-center justify-center gap-5 bg-gray-900">
        <input type="text" v-model="username" placeholder="Username" class="placeholder:text-center text-white text-center  outline-none py-2 bg-transparent border-b-2 px-20 mt-10 " required>
        <input type="password" v-model="password" placeholder="Password" class="placeholder:text-center text-white text-center  outline-none py-2 bg-transparent border-b-2 px-20 " required>
        <button type="submit" class="text-gray-900 px-10 mb-10 mt-10 py-2 rounded-sm text-lg font-bold bg-white ">Login</button>
        <div class="mb-[40px] flex flex-row items-center justify-center gap-3">
        <p class="text-gray-500">Not a user ?</p>
        <router-link to="/signup" class="text-white">Sign Up</router-link>
       </div>
      </form>
    </div>
    <p v-if="errormessage">{{ errormessage }}</p>
  </div>
</template>
