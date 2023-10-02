<template>
  <div class="todo-app">
    <h1 class="project-title">{{ projectName }}</h1>
    <div class="input-container">
      <input
        v-model="newTask"
        @keyup.enter="addTask"
        placeholder="Add a task"
        class="task-input"
      />
      <button @click="addTask" class="add-button">Add</button>
    </div>
    <div class="controls">
      <button @click="exportProject" class="export-button">Export</button>
      <button @click="importProject" class="import-button">Import</button>
      <input
        v-model="importedProject"
        placeholder="Paste exported project JSON here"
        class="import-input"
      />
      <input
        v-model="searchQuery"
        placeholder="Search tasks"
        class="search-input"
      />
    </div>
    <ul class="task-list">
      <li v-for="(task, index) in filteredTasks" :key="index" class="task-item">
        <input type="checkbox" v-model="task.done" class="checkbox" />
        <span v-if="!task.editing" class="task-text">{{ task.text }}</span>
        <input
          v-else
          v-model="task.text"
          @blur="saveTask(index)"
          class="task-edit-input"
        />
        <button @click="toggleEdit(index)" class="edit-button">
          {{ task.editing ? "Save" : "Edit" }}
        </button>
        <button @click="deleteTask(index)" class="delete-button">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      projectName: "My Project",
      newTask: "",
      tasks: [],
      importedProject: "",
      searchQuery: "",
    };
  },
  computed: {
    filteredTasks() {
      return this.tasks.filter((task) =>
        task.text.toLowerCase().includes(this.searchQuery.toLowerCase())
      );
    },
  },
  mounted() {
    this.loadFromLocalStorage();
  },
  methods: {
    saveProjectName() {
      localStorage.setItem("projectName", this.projectName);
    },
    addTask() {
      if (this.newTask.trim() !== "") {
        this.tasks.push({ text: this.newTask, done: false, editing: false });
        this.newTask = "";
        this.saveToLocalStorage();
      }
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
      this.saveToLocalStorage();
    },
    toggleEdit(index) {
      this.tasks[index].editing = !this.tasks[index].editing;
    },
    saveTask(index) {
      this.tasks[index].editing = false;
      this.saveToLocalStorage();
    },
    saveToLocalStorage() {
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    loadFromLocalStorage() {
      this.projectName =
        localStorage.getItem("projectName") || this.projectName;
      this.tasks = JSON.parse(localStorage.getItem("tasks")) || [];
    },
    exportProject() {
      const exportedProject = {
        projectName: this.projectName,
        tasks: this.tasks,
      };
      this.importedProject = JSON.stringify(exportedProject);
    },
    importProject() {
      try {
        const importedProject = JSON.parse(this.importedProject);
        if (importedProject.projectName && importedProject.tasks) {
          this.projectName = importedProject.projectName;
          this.tasks = importedProject.tasks;
          this.importedProject = "";
          this.saveToLocalStorage();
        } else {
          alert("Invalid project data.");
        }
      } catch (error) {
        alert("Invalid JSON data.");
      }
    },
  },
};
</script>

<style scoped>
.todo-app {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f7f7f7;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  font-family: Arial, sans-serif;
}

.project-title {
  font-size: 24px;
  margin-bottom: 20px;
}

.input-container {
  display: flex;
  align-items: center;
}

.task-input {
  flex: 1;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.add-button {
  margin-left: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 8px 16px;
  cursor: pointer;
}

.controls {
  display: flex;
  gap: 0.5rem;
  align-items: center;
  margin-top: 20px;
}

.export-button,
.import-button {
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 8px 16px;
  cursor: pointer;
}

.import-input,
.search-input {
  flex: 1;
  padding: 8px;
  margin-left: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

.task-list {
  list-style-type: none;
  padding: 0;
}

.task-item {
  display: flex;
  align-items: center;
  margin-top: 10px;
  background-color: white;
  padding: 10px;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.checkbox {
  margin-right: 10px;
}

.task-text {
  flex: 1;
}

.task-edit-input {
  flex: 1;
  padding: 4px;
}

.edit-button,
.delete-button {
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 4px 8px;
  cursor: pointer;
  margin-left: 10px;
}

@media screen and (max-width: 480px) {
  .todo-app {
    padding: 10px;
  }

  .project-title {
    font-size: 20px;
  }

  .input-container {
    flex-direction: column;
  }

  .add-button {
    margin-top: 10px;
  }

  .controls {
    flex-direction: column;
  }

  .import-input,
  .search-input {
    margin-top: 10px;
  }

  .task-item {
    flex-direction: column;
    text-align: center;
    padding: 10px 0;
  }

  .checkbox {
    margin-right: 0;
    margin-bottom: 10px;
  }

  .edit-button,
  .delete-button {
    margin-top: 5px;
  }
}
</style>
