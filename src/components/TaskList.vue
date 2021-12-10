<template>
  <h1>toDo App</h1>

  <label for="search">Buscar</label>
  <input type="text" v-model="taskFinder" @change="search" id="search" />
  <label for="hideCompleted">Ocultar tasks completadas</label>
  <input type="checkbox" v-model="hideCompletedTasks" id="hideCompleted" />
  <select name="order-select" v-model="sortBy" id="order-select">
    <option value="asc">Ascendente</option>
    <option value="desc">Descendente</option>
  </select>

  <div class="table-wrapper">
    <table class="table-tasks">
      <tr>
        <th></th>
        <th>Id</th>
        <th>Nombre</th>
        <th>Fecha de creación</th>
        <th>Completado en</th>
        <th>Acciones</th>
      </tr>
      <tr
        v-for="(task, $index) in computedTasks"
        :key="task.id"
        :class="{ completed: task.completed_at }"
      >
        <td>
          <input
            type="checkbox"
            :checked="task.completed_at"
            @change="checkboxChanged($index)"
          />
        </td>
        <td>
          {{ task.id }}
        </td>
        <td>
          {{ task.name }}
        </td>
        <td>
          {{ formatDate(task.created_at) }}
        </td>
        <td>
          {{ formatDate(task.completed_at) }}
        </td>
        <td>
          <button @click="remove($index)">Eliminar</button>
        </td>
      </tr>
      <tr>
        <td></td>
        <td colspan="4">
          <input type="text" v-model="newTask" />
        </td>
        <td>
          <button @click="add" :disabled="newTask == ''">Añadir</button>
        </td>
      </tr>
    </table>
  </div>
</template>

<script>
export default {
  name: "TaskList",
  mounted() {    
    this.loadLocalStorage()    
  },
  data() {
    return {
      tasks: [],
      newTask: "",
      taskFinder: "",
      hideCompletedTasks: "",
      sortBy: "asc",
    };
  },
  computed: {
    computedTasks() {
      const filteredTasks = this.tasks.filter((task) => {
        if (this.taskFinder) {
          if (!task.name.includes(this.taskFinder)) {
            return false;
          }
        }

        if (this.hideCompletedTasks) {
          return task.completed_at == null;
        }
        return true;
      });
      if(this.sortBy == "asc") {
        return filteredTasks
      } else {
        return filteredTasks.reverse();
      }
    },
  },
  methods: {
    remove: function ($index) {
      this.tasks.splice($index, 1);
      this.saveLocaleStorage()
    },
    add: function () {
      this.tasks.push({
        id: this.tasks.length + 1,
        name: this.newTask,
        created_at: new Date(),
        completed_at: null,
      });
      this.newTask = "";
      this.saveLocalStorage()
    },
    formatDate: function (date) {
      if (date == null) {
        return "";
      }
      return date.toLocaleString();
    },
    checkboxChanged: function (index) {
      const task = this.tasks[index];

      if (task.completed_at != null) {
        task.completed_at = null;
      } else {
        task.completed_at = new Date();
      }
    },
    loadLocalStorage: function () {
      const payload = localStorage.getItem("misTasks")
      this.tasks = payload ? JSON.parse(payload) : []
    },
    saveLocalStorage: function () {
      const payload = JSON.stringify(this.tasks)
      localStorage.setItem("misTasks", payload)
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1 {
  margin: 20px 0;
  text-align: center;
}

.table-wrapper {
  display: flex;
  justify-content: center;
}

.table-tasks td,
.table-tasks th {
  padding: 5px;
}

.table-tasks .completed {
  color: rgb(164, 170, 164);
}
</style>

