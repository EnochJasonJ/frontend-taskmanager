<script setup>
import { ref , defineEmits} from 'vue';
import axios from 'axios';
const task = ref("");
const emits = defineEmits(['taskAdded']);
const backendURL = "https://backend-taskmanager-5aqf.onrender.com";
const addTask = async () => {
  try {
    const token = localStorage.getItem("access_token");
    if(!token){
      throw new Error("No token found");
    }
    await axios.post(`${backendURL}/create-tasks/`,{
    name: task.value,
    },{
      headers: {
        Authorization: `Bearer ${token}`,
      }
    });
    task.value = '';
    console.log('Task added successfully');
    emits('taskAdded');
  } catch (error) {
    console.log(error);
  }
}
</script>

<template>
    <div class="bg-white w-full h-[100px] flex -px-1 lg:px-10 items-center justify-center">
      <form @submit.prevent="addTask" autocomplete="off">
        <div class="flex flex-row items-center justify-center gap-2 lg:gap-10">
          <div>
            <input type="text" v-model="task" name="task" id="task" placeholder="Task name" class="placeholder:text-center border-2 py-2 px-10 rounded-md outline-none" required>
          </div>
          <div>
            <button type="submit" class="bg-black text-blue-200 px-2 lg:px-5 py-2 rounded-md">Add Task</button>
          </div>
        </div>
      </form>
    </div>
</template>
