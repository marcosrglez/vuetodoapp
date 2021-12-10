<template>
  <div class="task-page" :class="{ 'dark-theme': darkMode }">
      <div class="dark-theme-container">
        <label for="dark-theme-checkbox">Modo oscuro</label>
        <input type="checkbox" v-model="darkMode" id="dark-theme-checkbox" />
      </div>
    <div class="page">
      <div class="container">
        <h1>toDo App</h1>
        <div class="search-bar">
          <label for="search">Buscar</label>
          <input
            type="text"
            v-model="taskFinder"
            @change="search"
            id="search"
          />
        </div>
        <div class="filter-options">
          <div class="Completed tasks">
            <input
              type="checkbox"
              v-model="hideCompletedTasks"
              id="hideCompleted"
            />
            <label for="hideCompleted">Ocultar completadas</label>
          </div>
          <div class="order-dropdown">
            <select name="order-select" v-model="sortBy" id="order-select">
              <option value="asc">Ascendente</option>
              <option value="desc">Descendente</option>
            </select>
          </div>
        </div>
        <table class="table-tasks">
          <thead>
            <tr>
              <th></th>
              <th>Id</th>
              <th>Nombre</th>
              <th>Fecha de creación</th>
              <th>Completado en</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
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
                <button class="button remove-button" @click="remove($index)">
                  Eliminar
                </button>
              </td>
            </tr>
            <tr>
              <td></td>
              <td colspan="4">
                <input
                  placeholder="Escribe una tarea"
                  id="new-task-name"
                  type="text"
                  v-model="newTask"
                />
              </td>
              <td>
                <button
                  class="button add-button"
                  @click="add"
                  :disabled="newTask == ''"
                >
                  Añadir
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  mounted() {
    this.loadLocalStorage();
  },
  data() {
    return {
      tasks: [],
      newTask: "",
      taskFinder: "",
      hideCompletedTasks: "",
      sortBy: "asc",
      darkMode: "",
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
      if (this.sortBy == "asc") {
        return filteredTasks;
      } else {
        return filteredTasks.reverse();
      }
    },
  },
  methods: {
    remove: function ($index) {
      this.tasks.splice($index, 1);
      this.saveLocalStorage();
    },
    add: function () {
      this.tasks.push({
        id: this.tasks.length + 1,
        name: this.newTask,
        created_at: new Date(),
        completed_at: null,
      });
      this.newTask = "";
      this.saveLocalStorage();
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
      let payload = localStorage.getItem("misTasks");
      let parsed = payload ? JSON.parse(payload) : [];
      parsed = parsed.map(function (t) {
        t.created_at = new Date(t.created_at);
        return t;
      });
      this.tasks = parsed;
    },
    saveLocalStorage: function () {
      const payload = JSON.stringify(this.tasks);
      localStorage.setItem("misTasks", payload);
    },
  },
};
</script>

<style>
#app,
body,
html {
  margin: 0;
  padding: 0;
}

.task-page {
  font-family: Roboto, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: rgb(248, 244, 244);
  padding: 0;
  margin: 0;
  height: 100vh;
}

.dark-theme {
  font-family: Roboto, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  background-color: rgb(66, 63, 63);
  padding: 0;
  margin: 0;
  height: 100vh;
}

.page {
  display: flex;
  justify-content: center;
}

h1 {
  margin: 20px 0;
  text-align: center;
}

.dark-theme-container {
  text-align: center;
  padding-top: 20px;
  color: rgb(28, 26, 26);
}

.container {
  max-width: 1024px;
  margin-top: 50px;
  background-color: white;
  box-shadow: 12px 12px 2px 1px rgb(211, 211, 211);
  padding: 35px;
}

#hideCompleted {
  margin-right: 10px;
}

.search-bar {
  display: flex;
  margin-bottom: 10px;
}

#search {
  width: 100%;
  margin-left: 10px;
}

.filter-options {
  display: flex;
  justify-content: space-between;
}

#new-task-name {
  width: 99%;
}

.button {
  border: 0;
  border-radius: 5px;
  padding: 3px;
  font-size: 0.85rem;
  width: 100%;
}

.remove-button {
  color: white;
  background-color: rgb(39, 36, 36);
}

.add-button {
  color: white;
  background-color: rgb(39, 36, 36);
}

.table-tasks {
  width: 100%;
}

thead th {
  color: rgb(163, 158, 158);
  font-weight: normal;
  font-size: 15px;
  text-align: left;
}

.table-tasks td,
.table-tasks th {
  padding: 5px;
}

.table-tasks .completed {
  color: rgb(164, 170, 164);
}

/** Dark theme */
.dark-theme .dark-theme-container {
  color: white;
}

.dark-theme .container {
  background-color: rgb(24, 23, 23);
  color: white;
  box-shadow: 0px 0px 0px;
}

.dark-theme .table-tasks {
  color: white;
}

.dark-theme .add-button {
    color: black;
  background-color: rgb(191, 184, 184);
}

.dark-theme .remove-button {
    color: black;
  background-color: rgb(191, 184, 184);
}
</style>