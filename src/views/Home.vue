<template>
  <AddTask v-show="showAddTask" @add-task="addTask" />
  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
</template>

<script>
import Tasks from "../components/Tasks";
import AddTask from "../components/AddTask";
export default {
  name: "Home",
  props: {
    showAddTask: Boolean,
  },
  components: {
    Tasks,
    AddTask,
  },
  data() {
    return {
      tasks: [],
    };
  },
  methods: {
    async addTask(task) {
      // -----------For JSON-SERVER----------\\

      const res = await fetch(
        "https://my-json-server.typicode.com/sharifmrahat/task-creator/tasks",
        {
          method: "POST",
          headers: {
            "Content-type": "application/json",
          },
          body: JSON.stringify(task),
        }
      );

      const data = await res.json();

      // -----------For LocalStorage----------\\

      this.tasks = [...this.tasks, data];
      console.log(this.tasks);
      localStorage.setItem("tasks", JSON.stringify(this.tasks));
    },
    async deleteTask(id) {
      if (confirm("Are you sure?")) {
        const res = await fetch(
          `https://my-json-server.typicode.com/sharifmrahat/task-creator/tasks/${id}`,
          {
            method: "DELETE",
          }
        );

        res.status === 200
          ? (this.tasks = this.tasks.filter((task) => task.id !== id))
          : alert("Error deleting task");
      }
    },
    async toggleReminder(id) {
      const taskToToggle = await this.fetchTask(id);
      const updTask = { ...taskToToggle, reminder: !taskToToggle.reminder };

      const res = await fetch(
        `https://my-json-server.typicode.com/sharifmrahat/task-creator/tasks/${id}`,
        {
          method: "PUT",
          headers: {
            "Content-type": "application/json",
          },
          body: JSON.stringify(updTask),
        }
      );

      const data = await res.json();

      this.tasks = this.tasks.map((task) =>
        task.id === id ? { ...task, reminder: data.reminder } : task
      );
    },
    async fetchTasks() {
      const res = await fetch(
        "https://my-json-server.typicode.com/sharifmrahat/task-creator/tasks"
      );

      const data = await res.json();

      return data;
    },
    async fetchTask(id) {
      const res = await fetch(
        `https://my-json-server.typicode.com/sharifmrahat/task-creator/tasks/${id}`
      );

      const data = await res.json();

      return data;
    },
  },
  async created() {
    this.tasks = await this.fetchTasks();
  },
};
</script>
