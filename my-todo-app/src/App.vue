<template>
  <div class="container">
    <!-- Header -->
    <header class="header">
      <div>
        <h1>ToDoList</h1>
      </div>
    </header>
    <!-- Input Section -->
    <div class="input-section">
      <input v-model="newTask" type="text" class="task-input" placeholder="请输入待办事项" @keyup.enter="addTask" />
      <button @click="addTask" class="submit-button">Submit</button>
    </div>
    <!-- Task Lists -->
    <div class="task-container">
      <!-- In Progress Tasks -->
      <div class="task-list">
        <h2>正在进行 ({{ inProgressTasks.length }})</h2>
        <div v-for="task in inProgressTasks" :key="task.id" class="task-item">
          <span v-if="!task.isEditing" class="task-text">{{ task.text }}</span>
          <input v-else v-model="task.editingText" type="text" class="edit-input" @keyup.enter="saveTask(task)" @blur="saveTask(task)" />
          <div class="task-buttons">
            <button v-if="!task.isEditing" @click="editTask(task)" class="edit-button">✏️</button>
            <button @click="deleteTask(task)" class="delete-button">🗑️</button>
            <button @click="completeTask(task)" class="complete-button">✔️</button>
          </div>
        </div>
      </div>
      <!-- Completed Tasks -->
      <div class="task-list">
        <h2>已经完成 ({{ completedTasks.length }})</h2>
        <div v-for="task in completedTasks" :key="task.id" class="task-item">
          <span class="completed-task">{{ task.text }}</span>
          <button @click="uncompleteTask(task)" class="undo-button">↩️</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue';

interface Task {
  id: number;
  text: string;
  completed: boolean;
  isEditing?: boolean;
  editingText?: string;
}

const newTask = ref('');
const tasks = ref<Task[]>([]);
let nextId = 1;

const inProgressTasks = computed(() => tasks.value.filter(task => !task.completed));
const completedTasks = computed(() => tasks.value.filter(task => task.completed));

const addTask = () => {
  if (newTask.value.trim()) {
    tasks.value.push({ id: nextId++, text: newTask.value.trim(), completed: false });
    newTask.value = '';
  }
};

const completeTask = (task: Task) => {
  const index = tasks.value.findIndex(t => t.id === task.id);
  if (index !== -1) {
    tasks.value[index].completed = true;
  }
};

const uncompleteTask = (task: Task) => {
  const index = tasks.value.findIndex(t => t.id === task.id);
  if (index !== -1) {
    tasks.value[index].completed = false;
  }
};

const editTask = (task: Task) => {
  task.isEditing = true;
  task.editingText = task.text;
};

const saveTask = (task: Task) => {
  if (task.editingText?.trim()) {
    task.text = task.editingText.trim();
  }
  task.isEditing = false;
};

const deleteTask = (task: Task) => {
  const index = tasks.value.findIndex(t => t.id === task.id);
  if (index !== -1) {
    tasks.value.splice(index, 1);
  }
};
</script>

<style scoped>
.container {
  min-height: 100vh;
  background-color: #f9fafb;
  padding: 20px;
}

.header {
  background-color: #3b82f6;
  color: white;
  padding: 16px;
  text-align: center;
  font-size: 24px;
  font-weight: bold;
}

.input-section {
  display: flex;
  gap: 10px;
  margin-top: 20px;
  justify-content: center;
}

.task-input {
  flex: 1;
  padding: 8px;
  border: 1px solid #d1d5db;
  border-radius: 8px;
}

.submit-button {
  background-color: #3b82f6;
  color: white;
  padding: 8px 16px;
  border-radius: 8px;
  cursor: pointer;
}

.submit-button:hover {
  background-color: #2563eb;
}

.task-container {
  display: flex;
  gap: 20px;
  margin-top: 30px;
  justify-content: center;
}

.task-list {
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  padding: 20px;
  width: 300px;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  background: #f3f4f6;
  border-radius: 6px;
  margin-top: 8px;
}

.task-text {
  flex: 1;
}

.completed-task {
  flex: 1;
  text-decoration: line-through;
  color: gray;
}

.task-buttons button {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 16px;
}

.edit-button {
  color: #6b7280;
}

.delete-button {
  color: red;
}

.complete-button {
  color: green;
}

.undo-button {
  color: #6b7280;
}

.edit-input {
  flex: 1;
  padding: 5px;
  border: 1px solid #d1d5db;
  border-radius: 4px;
}
</style>
