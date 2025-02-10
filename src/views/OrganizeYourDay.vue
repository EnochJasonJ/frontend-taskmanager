<script setup>
import TaskForm from '../components/TaskForm.vue';
import { ref , onMounted} from 'vue';
import axios from 'axios';
const data = ref(null);
const fetchTasks = async () => {
  try {
    const token = localStorage.getItem("access_token");
    if(!token){
      throw new Error("No token found");
    }
    const response = await axios.get('http://127.0.0.1:8000/list-tasks/',{
      headers: {
        Authorization: `Bearer ${token}`,
      }
    })
    data.value = response.data;
    console.log(data.value);
  } catch (error) {
    console.log(error);
  }
}
  const deleteTask = async (id) => {
    try {
      const token = localStorage.getItem("access_token");
      if(!token){
        throw new Error("No token found");
      }
      await axios.delete(`http://127.0.0.1:8000/delete-tasks/${id}/`,{
        headers:{
          Authorization: `Bearer ${token}`
        }
      });
    }
    catch (error) {
      console.log(error);
    }
    fetchTasks();
  }
onMounted(fetchTasks);
</script>

<template>
    <div  class="flex flex-col items-center justify-center">
      <h1>Organize your day</h1>
      <ul v-if="data" class="mb-[100px]">
        <li v-for="post in data" :key="post.id" class="flex flex-row gap-5 p-5 w-[400px] items-center justify-between bg-red-300 mb-[25px] mt-[25px] rounded-md hover:cursor-pointer hover:scale-[110%] transition-all hover:duration-1000 ease-in-out">
          <h1 class="font-bold text-xl">{{ post.name }}</h1>
          <div class="flex flex-row gap-5">
            <i class="pi pi-trash bg-black text-white p-3 rounded-full" @click="deleteTask(post.id)"></i>
            <RouterLink :to="`/edit/${post.id}`" class="pi pi-pencil bg-black text-white p-3 rounded-full" ></RouterLink>
          </div>
        </li>
      </ul>
      <TaskForm class="fixed bottom-0" @task-added="fetchTasks" />
    </div>
</template>
