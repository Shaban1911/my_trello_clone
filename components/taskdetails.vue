<template>
  <div style="   margin: 10px;
    padding: 20px;">
    <div class="task-details-header">
      <h1 class="text-2xl font-bold">{{ task.title }}</h1>
      <p>Status: {{ getStatusTitle(task.status) }}</p>
    </div>

    <div class="task-details-body">
      <p>{{ task.description }}</p>

      <div class="task-details-buttons">
        <button @click="editTask" class="edit-button">Edit Task</button>
        <button @click="deleteTask" class="delete-button">Delete Task</button>
      </div>
    </div>

    <!-- Modal for editing task -->
    <div v-if="isEditing" class="modal-overlay">
      <div class="modal-content">
        <label for="editTitle">Title:</label>
        <input v-model="editedTask.title" id="editTitle" class="input-field" />

        <label for="editDescription">Description:</label>
        <textarea v-model="editedTask.description" id="editDescription" class="input-field"></textarea>

        <label for="editStatus">Status:</label>
        <select v-model="editedTask.status" id="editStatus" class="input-field">
          <option v-for="status in statuses" :key="status.id" :value="status.id">{{ status.title }}</option>
        </select>

        <button @click="saveEdit" class="action-button save-button">Save</button>
        <button @click="cancelEdit" class="action-button cancel-button">Cancel</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      task: {
        id: 1,
        title: "Card 1",
        description: "Task description",
        status: 1,
      },
      isEditing: false,
      editedTask: { id: 1, title: "", description: "", status: 1 },
      statuses: [
        { id: 1, title: "Not started" },
        { id: 2, title: "In progress" },
        { id: 3, title: "Completed" },
      ],
    };
  },
  methods: {
    editTask() {
      // Copy the task to the editedTask object
      this.editedTask = { ...this.task };
      this.isEditing = true;
    },
    saveEdit() {
      // Save the edited task to local storage
      const updatedTasks = JSON.parse(localStorage.getItem("tasks")) || [];

      const taskIndex = updatedTasks.findIndex((task) => task.id === this.task.id);

      if (taskIndex !== -1) {
        // Update the task in the array
        updatedTasks[taskIndex] = { ...this.editedTask };

        // Save the updated array to local storage
        localStorage.setItem("tasks", JSON.stringify(updatedTasks));

        // Update the current task with the edited task
        this.task = { ...this.editedTask };

        // Exit editing mode
        this.isEditing = false;
      }
    },
    cancelEdit() {
      // Cancel the editing
      this.isEditing = false;
    },
    deleteTask() {
      // Delete the task from local storage
      const confirmDelete = confirm("Are you sure you want to delete this task?");

      if (confirmDelete) {
        const updatedTasks = JSON.parse(localStorage.getItem('tasks')) || [];

        // Remove the task from the array
        const remainingTasks = updatedTasks.filter(task => task.id !== this.task.id);

        // Save the updated array to local storage
        localStorage.setItem('tasks', JSON.stringify(remainingTasks));

        // Redirect to the project board or any other appropriate route after deletion
        this.$router.push({ name: 'ProjectBoard' });
      }
    },
    getStatusTitle(statusId) {
      const status = this.statuses.find(s => s.id === statusId);
      return status ? status.title : '';
    },
  },
  created() {
    // Check if running in a browser environment and if localStorage is available
    if (typeof window !== 'undefined' && typeof window.localStorage !== 'undefined') {
      const tasks = JSON.parse(localStorage.getItem('tasks')) || [];
      const taskId = parseInt(this.$route.params.taskId);

      const task = tasks.find((task) => task.id === taskId);

      if (task) {
        this.task = { ...task };
      }
    } else {
      console.error('localStorage is not available.');
      // Handle the lack of localStorage as needed
    }
  },
};
</script>

<style scoped>
.task-details-header {
  background-color: #3498db; /* Example color: Belize Hole */
  color: #fff;
  padding: 20px;
  border-radius: 5px;
  margin-bottom: 20px;
}

.task-details-body {
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-bottom: 20px;
}

.task-details-buttons {
  display: flex;
  justify-content: space-between;
}

.action-button {
  padding: 10px;
  cursor: pointer;
  border: none;
  border-radius: 5px;
}

.edit-button {
  margin-top:10px;
  background-color: #2ecc71; /* Example color: Emerald */
  color: #fff;
}

.delete-button {
  margin-top:10px;
  background-color: #e74c3c; /* Example color: Alizarin */
  color: #fff;
}

.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background-color: #fff;
  padding: 20px;
  border-radius: 5px;
}

.input-field {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.save-button {
  background-color: #3498db; /* Example color: Belize Hole */
  color: #fff;
}

.cancel-button {
  background-color: #95a5a6; /* Example color: Concrete */
  color: #fff;
}
div#__layout {
    margin: 10px;
    padding: 20px;
}

</style>
