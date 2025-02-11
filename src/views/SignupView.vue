<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import axios from 'axios';
const backendURL = "https://backend-taskmanager-5aqf.onrender.com";
const router = useRouter();
const username = ref('');
const password = ref('');
const errormessage = ref('');
const successmessage = ref('');

const register = async () => {
  try {
    const response = await axios.post(`${backendURL}/register/`,{
      username: username.value,
      password: password.value
    });

    localStorage.setItem("access_token", response.data.access);
    localStorage.setItem("refresh_token", response.data.refresh);
    successmessage.value = "Account created! Redirecting...";
    setTimeout(()=> {
      router.push("/organize/")
    }, 2000);
  } catch (error) {
      errormessage.value = error.response?.data?.error || "Signup Failed";
  }
}

</script>

<template>
  <div class="flex flex-col items-center justify-center">
    <h2>Signup</h2>
    <form @submit.prevent="register" class="flex w-[400px] mt-20 rounded-lg flex-col items-center justify-center gap-5 bg-gray-900">
        <input type="text" v-model="username" placeholder="Username" class="placeholder:text-center text-white text-center  outline-none py-2 bg-transparent border-b-2 px-20 mt-10 " required>
        <input type="password" v-model="password" placeholder="Password" class="placeholder:text-center text-white text-center  outline-none py-2 bg-transparent border-b-2 px-20 " required>
        <button type="submit" class="text-gray-900 px-10 mb-10 mt-10 py-2 rounded-sm text-lg font-bold bg-white ">Sign UP</button>
        <div class="mb-[40px] flex flex-row items-center justify-center gap-3">
        <p class="text-gray-500">Already a user ?</p>
        <router-link to="/login" class="text-white">Sign In</router-link>
       </div>
      </form>
    <p v-if="errormessage">{{ errormessage }}</p>
    <p v-if="successmessage">{{ successmessage }}</p>
  </div>
</template>
