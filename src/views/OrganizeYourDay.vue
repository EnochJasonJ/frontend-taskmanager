<script setup>
import TaskForm from '../components/TaskForm.vue';
import { ref , onMounted} from 'vue';
import axios from 'axios';
const data = ref(null);
const backendURL = "https://backend-taskmanager-5aqf.onrender.com";

const fetchTasks = async () => {
  try {
    const token = localStorage.getItem("access_token");
    if(!token){
      throw new Error("No token found");
    }
    const response = await axios.get(`${backendURL}/list-tasks/`,{
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
      await axios.delete(`${backendURL}/delete-tasks/${id}/`,{
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
  const toggleTaskCompletion = async (task) => {
    try {
      const token = localStorage.getItem("access_token");
      if(!token){
        throw new Error("No token found");
      }
      await axios.patch(`${backendURL}/update-tasks/${task.id}/`,{
        is_completed: !task.is_completed
      },{
        headers: {
          Authorization: `Bearer ${token}`
        }
      });
      task.is_completed = !task.is_completed;
    } catch (error) {
      console.log(error);
    }
  }
onMounted(fetchTasks);
</script>

<template>
  <div class="flex flex-col items-center justify-center">
    <h1 class="font-bold text-2xl lg:text-4xl">Organize your day</h1>
    <ul v-if="data" class="mb-[100px]">
      <li v-for="task in data" :key="task.id" class="flex flex-row gap-5 p-5 w-[380px] h-[80px] lg:w-[400px] items-center justify-between bg-blue-200 mb-[25px] mt-[25px] rounded-md shadow-md hover:cursor-pointer hover:scale-[110%] transition-all hover:duration-1000 ease-in-out">
        <div class="flex items-center gap-3">
          <label class="flex items-center gap-3 cursor-pointer">
            <input
              type="checkbox"
              :checked="task.is_completed"
              @change="toggleTaskCompletion(task)"
              class="hidden peer"
            />
            <div
              class="w-6 h-6 border-[2px] border-black rounded-full flex items-center justify-center peer-checked:bg-blue-400 peer-checked:border-red-200 transition-all duration-300 ease-in-out"
            >
              <i class="pi pi-check text-white peer-checked:opacity-100 opacity-0 transition-all duration-300"></i>
            </div>
          </label>
          <h1 :class="{ 'line-through': task.is_completed }" class="font-bold text-[16px] lg:text-xl">{{ task.name }}</h1>
        </div>
        <div class="flex flex-row gap-5">
          <i class="pi pi-trash bg-black text-blue-200 p-3 rounded-full" @click="deleteTask(task.id)"></i>
          <RouterLink :to="`/edit/${task.id}`" class="pi pi-pencil bg-black text-blue-200 p-3 rounded-full"></RouterLink>
        </div>
      </li>
    </ul>
    <TaskForm class="fixed bottom-0" @task-added="fetchTasks" />
  </div>
</template>


<style>
.line-through {
  text-decoration: line-through;
  color: rgb(0, 0, 0);
}
</style>
