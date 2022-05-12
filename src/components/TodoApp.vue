<template>
  <div class="container" style="max-width: 600px">
    <!-- heading start -->
    <h2 class="text-center mt-5">My vue to do app</h2>
    <!-- input -->
    <div class="d-flex mt-5">
      <input
        type="text"
        placeholder="Enter task"
        class="w-100 form control"
        v-model="task"
      />
      <button class="btn btn-warning rounded-0" @click="submitTask">
        SUBMIT
      </button>
    </div>
    <!-- tasks table -->
    <table class="table table-bordered mt-5">
      <thead>
        <tr>
          <th scope="col">Task</th>
          <th scope="col" style="width: 120px">Status</th>
          <th scope="col" class="text-center">#</th>
          <th scope="col" class="text-center">#</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(task, index) in tasks" :key="index">
          <td>
            <span :class="{ 'line-through': task.status === 'finished' }">
              {{ capitalizeFirstChar(task.name) }}
            </span>
          </td>
          <td>
            <span
              class="pointer noselect"
              @click="changeStatus(index)"
              :class="{
                'text-danger': task.status === 'to-do',
                'text-success': task.status === 'finished',
                'text-warning': task.status === 'in-progress',
              }"
            >
              {{ capitalizeFirstChar(task.status) }}
            </span>
          </td>
          <td class="text-center">
            <div @click="deleteTask(index)">
              <span
                class="iconify"
                data-icon="fa6-solid:trash-can-arrow-up"
              ></span>
            </div>
          </td>
          <td class="text-center">
            <div @click="editTask(index)">
              <span class="iconify" data-icon="fa6-solid:pen"></span>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: "TodoApp",
  props: {
    msg: String,
  },
  data() {
    return {
      STORAGE_KEY: "todo-app-storage",
      task: "",
      editedTask: null,
      statuses: ["to-do", "in-progress", "finished"],
      //  Status could be: 'to-do' / 'in-progress' / 'finished' in the input
      tasks: [],
    };
  },
  created() {
    this.tasks = JSON.parse(localStorage.getItem(this.STORAGE_KEY) || "[]");
    console.log(this.tasks);
  },
  methods: {
    // Capitalize first character
    capitalizeFirstChar(str) {
      return str.charAt(0).toUpperCase() + str.slice(1);
    },
    // change status bar
    changeStatus(index) {
      let newIndex = this.statuses.indexOf(this.tasks[index].status);
      if (++newIndex > 2) newIndex = 0;
      this.tasks[index].status = this.statuses[newIndex];
      localStorage.setItem(this.STORAGE_KEY, JSON.stringify(this.tasks));
    },
    // delete task
    deleteTask(index) {
      this.tasks.splice(index, 1);
      localStorage.setItem(this.STORAGE_KEY, JSON.stringify(this.tasks));
    },
    // add task
    editTask(index) {
      this.task = this.tasks[index].name;
      this.editedTask = index;
      localStorage.setItem(this.STORAGE_KEY, JSON.stringify(this.tasks));
    },
    // add & update task
    submitTask() {
      if (this.task.length === 0) return;
      //  update the task
      if (this.editedTask != null) {
        this.tasks[this.editedTask].name = this.task;
        this.editedTask = null;
      } else {
        //  add new task
        this.tasks.push({
          name: this.task,
          status: "new entry",
        });
        localStorage.setItem(this.STORAGE_KEY, JSON.stringify(this.tasks));
      }
      this.task = "";
    },
  },
};
</script>

<style>
.pointer {
  cursor: pointer;
}

.noselect {
  -webkit-touch-callout: none; /* iOS Safari */
  -webkit-user-select: none; /* Safari */
  -khtml-user-select: none; /* Konqueror HTML */
  -moz-user-select: none; /* Old versions of Firefox */
  -ms-user-select: none; /* Internet Explorer/Edge */
  user-select: none; /* Non-prefixed version, currently supported by Chrome, Edge, Opera and Firefox */
}
.line-through {
  text-decoration: line-through;
}
</style>
