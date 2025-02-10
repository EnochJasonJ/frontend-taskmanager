<script setup>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import axios from 'axios';

const route = useRoute();
const router = useRouter();
const task = ref('');
const fetchTask = async () => {
  try {
    const token = localStorage.getItem("access_token");
    if(!token){
      throw new Error("No token found");
    }
      const response = await axios.get(`http://127.0.0.1:8000/task/${route.params.id}`,{
        headers:{
          Authorization: `Bearer ${token}`
        }
      });
      task.value = response.data.name;
      console.log(response.data);
  } catch (error) {
    console.log(error);console.log(error)
  }
}

const updateTask = async () => {
  try {
    const token = localStorage.getItem("access_token");
    if(!token){
      throw new Error("No token found");
    }
    await axios.put(`http://127.0.0.1:8000/update-tasks/${route.params.id}/`, {
      name: task.value
    },{
      headers:{
        Authorization: `Bearer ${token}`
      }
    });
    router.push('/organize/');
  } catch (error) {
      console.log(error);
  }
}
onMounted(fetchTask);
</script>

<template>
  <div class="flex flex-col items-center justify-center">
    <h1>Edit Page</h1>
    <form @submit.prevent="updateTask">
      <div class="flex flex-row items-center justify-center gap-10 mt-32">
            <div>
              <input type="text" v-model="task" name="task" id="task" placeholder="Task name" class="placeholder:text-center border-2 py-2 px-10 rounded-md outline-none" required>
            </div>
            <div>
              <button type="submit" class="bg-black text-white px-5 py-2 rounded-md">Update Task</button>
            </div>
          </div>

    </form>
  </div>
</template>
