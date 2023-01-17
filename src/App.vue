<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref(null);

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    alert("You must fill in all the details");
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = "";
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos") || []);
});
</script>

<template>
  <main>
    <div>
      <h2>
        Hello,
        <input
          class="input-name"
          type="text"
          placeholder="Your name"
          v-model="name"
          maxlength="12"
          style="text-transform: capitalize"
        />
      </h2>
    </div>
    <form @submit.prevent="addTodo">
      <div>
        <p>What's on your todo list?</p>
        <input
          class="input-text"
          type="text"
          placeholder="e.g. make a video"
          v-model="input_content"
          maxlength="60"
        />
      </div>
      <div>
        <p>Pick a category:</p>
        <input
          type="radio"
          name="category"
          value="business"
          class="business"
          v-model="input_category"
        /><label for="personal">Business</label>
        <input
          type="radio"
          name="personal"
          value="personal"
          class="personal"
          v-model="input_category"
        />
        <label for="personal">Personal</label>
      </div>
      <input type="submit" value="Add" />
    </form>
    <div>
      <p>Todo List:</p>
      <div v-for="todo in todos_asc">
        <div :class="`card-${todo.category}`">
          <input type="checkbox" v-model="todo.done" />
          <input
            :class="`todo-content-${todo.done}`"
            type="text"
            v-model="todo.content"
            maxlength="60"
          />
          <button class="delete-btn" @click="removeTodo(todo)">Delete</button>
        </div>
      </div>
    </div>
  </main>
</template>
