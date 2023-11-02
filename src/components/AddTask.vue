<script setup>
import { ref } from "vue";
import axios from "axios";
import { v4 as uuidv4 } from "uuid";

const input_content = ref("");
const input_category = ref(null);

const props = defineProps({
  show: Boolean,
  todos: Array,
});

const addTask = async () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  const newId = uuidv4();

  const newTask = {
    id: newId,
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  };

  try {
    const response = await axios.post("http://localhost:3000/todos", newTask);
    console.log("New task added:", response.data);
    props.todos.unshift(response.data);
    input_content.value = "";
    input_category.value = null;
  } catch (error) {
    console.error("Error adding task:", error);
  }
};
</script>
<template>
  <div v-if="show">
    <section class="create-todo">
      <button class="button-close" @click="$emit('close')">Return</button>

      <form @submit.prevent="addTask">
        <input
          type="text"
          placeholder="Write your task here !"
          v-model="input_content"
        />
        <h4>Pick a category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="urgent"
              v-model="input_category"
            />
            <span class="bubble urgent"></span>
            <div>Urgent</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="important"
              v-model="input_category"
            />
            <span class="bubble important"></span>
            <div>Important</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category3"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category4"
              value="study"
              v-model="input_category"
            />
            <span class="bubble study"></span>
            <div>Study</div>
          </label>
        </div>

        <input type="submit" value="Save" />
      </form>
    </section>
  </div>
</template>
