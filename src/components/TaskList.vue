<script setup>
import axios from "axios";

const props = defineProps({
  filteredTodos: Array,
});

const updateTaskStatus = async (todoId, done) => {
  try {
    // Send a PATCH or PUT request to update the task's "completed" status
    await axios.patch(`http://localhost:3000/todos/${todoId}`, { done });

    // Optionally, you can handle the response or update your component's data
  } catch (error) {
    console.error("Error updating task status:", error);
  }
};
</script>

<template>
  <div class="list">
    <TransitionGroup name="list">
      <div
        v-for="todo in props.filteredTodos"
        :class="`todo-item ${todo.done && 'done'}`"
        :key="todo"
      >
        <label>
          <input
            type="checkbox"
            v-model="todo.done"
            @change="updateTaskStatus(todo.id, todo.done)"
          />
          <span :class="`bubble ${todo.category}`"></span>
        </label>

        <div class="todo-content">
          <input type="text" v-model="todo.content" />
        </div>

        <div class="actions">
          <button @click="$emit('removeTodo', todo)">
            <img src="src/assets/corbeille.png" />
          </button>
        </div>
      </div>
    </TransitionGroup>
  </div>
</template>
