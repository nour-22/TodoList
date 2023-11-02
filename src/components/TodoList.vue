<script setup>
import axios from "axios";
import { ref, onMounted, computed, watch } from "vue";
import AddTask from "./AddTask.vue";
import taskList from "./TaskList.vue";

const todos = ref([]);
const showAddScreen = ref(false);

const visibility = ref("all");
const filteredTodos = computed(() => filters[visibility.value](todos.value));

const filters = {
  all: (todos) => todos,
  active: (todos) => todos.filter((todo) => !todo.done),
  completed: (todos) => todos.filter((todo) => todo.done),
};

function onHashChange() {
  const route = window.location.hash.replace(/#\/?/, "");
  if (filters[route]) {
    visibility.value = route;
  } else {
    window.location.hash = "";
    visibility.value = "all";
  }
}

window.addEventListener("hashchange", onHashChange);
onHashChange();

const removeTodo = async (todo) => {
  try {
    await axios.delete(`http://localhost:3000/todos/${todo.id}`);

    todos.value = todos.value.filter((t) => t !== todo);
  } catch (error) {
    console.error("Error deleting task:", error);
  }
};

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

onMounted(async () => {
  try {
    const response = await axios.get("http://localhost:3000/todos");
    todos.value = response.data;
    console.log(todos);
  } catch (error) {
    console.error("Error fetching tasks:", error);
  }
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">Welcome to your ToDo List</h2>
    </section>
    <button
      v-if="!showAddScreen"
      class="btn"
      id="show-modal"
      @click="showAddScreen = true"
    >
      Add a task
    </button>
    <AddTask
      :show="showAddScreen"
      @close="showAddScreen = false"
      :todos="todos"
    />
    <section class="todo-list">
      <div class="actions">
        <button>
          <a href="#/all" :class="{ selected: visibility === 'all' }">All</a>
        </button>
        <button>
          <a href="#/active" :class="{ selected: visibility === 'active' }"
            >Active</a
          >
        </button>
        <button>
          <a
            href="#/completed"
            :class="{ selected: visibility === 'completed' }"
            >Completed</a
          >
        </button>
      </div>
      <taskList :filteredTodos="filteredTodos" @removeTodo="removeTodo" />
    </section>
  </main>
</template>
