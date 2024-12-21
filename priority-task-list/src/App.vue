<template>
  <div>
    <h1>Priority Task List</h1>
    <form @submit.prevent="addTask">
      <input v-model="newTaskName" placeholder="Task Name" required />
      <select v-model="newTaskPriority" required>
        <option disabled value="">Select Priority</option>
        <option>High</option>
        <option>Medium</option>
        <option>Low</option>
      </select>
      <button type="submit">Add Task</button>
    </form>

    <div v-for="priority in priorities" :key="priority">
      <h2>{{ priority }} Priority</h2>
      <p v-if="!tasks[priority].length">No tasks available in this category</p>
      <ul>
        <li v-for="task in sortedTasks(priority)" :key="task.name">{{ task.name }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue';

export default {
  setup() {
    const newTaskName = ref('');
    const newTaskPriority = ref('');
    const tasks = ref({
      High: [],
      Medium: [],
      Low: []
    });

    const priorities = ['High', 'Medium', 'Low'];

    const addTask = () => {
      if (newTaskName.value && newTaskPriority.value) {
        tasks.value[newTaskPriority.value].push({ name: newTaskName.value });
        saveTasks();
        newTaskName.value = '';
        newTaskPriority.value = '';
      }
    };

    const sortedTasks = (priority) => {
      return tasks.value[priority].slice().sort((a, b) => a.name.localeCompare(b.name));
    };

    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value));
    };

    const loadTasks = () => {
      const savedTasks = localStorage.getItem('tasks');
      if (savedTasks) {
        tasks.value = JSON.parse(savedTasks);
      }
    };

    onMounted(() => {
      loadTasks();
    });

    return {
      newTaskName,
      newTaskPriority,
      tasks,
      priorities,
      addTask,
      sortedTasks
    };
  }
};
</script>

<style scoped>
form {
  margin-bottom: 20px;
}
</style>