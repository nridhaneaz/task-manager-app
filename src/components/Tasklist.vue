<template>
    <div class="task-list">
      <h1>Task Manager</h1>
  
      
      <button @click="toggleForm" class="toggle-btn">
        {{ showForm ? 'Hide Task Form' : 'Show Task Form' }}
      </button>
  
      
      <div v-if="showForm" class="task-form">
        <input
          v-model="newTask"
          @keyup.enter="addTask"
          placeholder="Type a task name"
          class="task-input"
        />
        <button @click="addTask" class="add-btn">Add Task</button>
      </div>
  
      
      <div class="filter">
        <label>
          <input
            type="checkbox"
            v-model="showOnlyCompleted"
          />
          Show only completed tasks
        </label>
      </div>
  
      
      <div v-if="filteredTasks.length === 0" class="no-tasks">
        <p>No tasks available</p>
      </div>
  
      
      <ul v-else class="task-list-container">
        <li
          v-for="(task, index) in filteredTasks"
          :key="index"
          :class="{ completed: task.completed }"
          class="task-item"
        >
          <span class="task-index">{{ index + 1 }}.</span>
          <span class="task-name">{{ task.name }}</span>
  
          
          <button @click="toggleTask(index)" class="complete-btn">
            {{ task.completed ? 'Undo' : 'Complete' }}
          </button>
  
          <button @click="removeTask(index)" class="delete-btn">Delete</button>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  import { reactive } from "vue";
  
  const taskState = reactive({
    tasks: JSON.parse(localStorage.getItem("tasks") || "[]"),
    addTask(name) {
      this.tasks.push({ name, completed: false });
      this.saveTasks();
    },
    toggleTask(index) {
      this.tasks[index].completed = !this.tasks[index].completed;
      this.saveTasks();
    },
    removeTask(index) {
      this.tasks.splice(index, 1);
      this.saveTasks();
    },
    saveTasks() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
  });
  
  export default {
    name: "TaskList",
    data() {
      return {
        newTask: "",
        showOnlyCompleted: false,
        showForm: false,
      };
    },
    computed: {
      tasks() {
        return taskState.tasks;
      },
      filteredTasks() {
        return this.showOnlyCompleted
          ? this.tasks.filter((task) => task.completed)
          : this.tasks;
      },
    },
    methods: {
      addTask() {
        if (this.validateTask(this.newTask)) {
          taskState.addTask(this.newTask.trim());
          this.newTask = "";
        } else {
          alert("Task name must be at least 3 characters long.");
        }
      },
      toggleTask(index) {
        taskState.toggleTask(index);
      },
      removeTask(index) {
        taskState.removeTask(index);
      },
      validateTask(taskName) {
        return taskName.trim().length >= 3;
      },
      toggleForm() {
        this.showForm = !this.showForm;
      },
    },
  };
  </script>
  
  <style scoped>
  .task-list {
    max-width: 400px;
    margin: auto;
    text-align: center;
  }
  
  
  .task-input {
    width: 100%;
    padding: 10px;
    font-size: 16px;
    border-radius: 5px;
    border: 1px solid #ccc;
    box-sizing: border-box;
    margin-bottom: 10px;
  }
  
  
  .add-btn {
    margin-top: 10px;
    padding: 8px 15px;
    background-color: #28a745;
    border: none;
    color: white;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .add-btn:hover {
    background-color: #218838;
  }
  
  .task-list-container {
    list-style: none;
    padding: 0;
    margin-top: 20px;
  }
  
  
  .task-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 8px;
    font-size: 14px;
    text-align: left;
    border-bottom: 1px solid #e0e0e0;
  }
  
  
  .task-index {
    font-weight: bold;
    margin-right: 10px;
    color: #555;
  }
  
  
  .task-name {
    flex-grow: 1;
    text-align: left;
  }
  
  
  li.completed .task-name {
    text-decoration: line-through;
    color: #808080;
    background-color: lightgreen;
    border: 2px solid red;
    padding: 4px;
    border-radius: 3px;
  }
  
  
  .toggle-btn {
    margin: 10px 0;
    padding: 8px 15px;
    background-color: #007bff;
    border: none;
    color: white;
    border-radius: 5px;
    cursor: pointer;
  }
  
  .toggle-btn:hover {
    background-color: #0056b3;
  }
  
  
  .complete-btn {
    background-color: transparent;
    border: 1px solid #007bff;
    color: #007bff;
    cursor: pointer;
    padding: 5px 10px;
    border-radius: 5px;
  }
  
  .complete-btn:hover {
    background-color: #007bff;
    color: white;
  }
  
  
  .delete-btn {
    background-color: transparent;
    border: none;
    color: red;
    cursor: pointer;
  }
  
  .delete-btn:hover {
    text-decoration: underline;
  }
  </style>
  