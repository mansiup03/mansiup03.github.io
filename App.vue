<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's Up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4>What's on your ToDo List?</h4>
        <input type="text" placeholder="e.g. make a video" v-model="input_content" />

        <h4>Pick a category</h4>
        <div class="options">

          <label> <input type="radio" name="category" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label> <input type="radio" name="category" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input type="submit" value="Add todo">
      </form>
    </section>

    <section class="todo-list">
      <h3>ToDo List</h3>
      <div class="filter-container">
        <label for="categoryFilter">Filter by Category:</label>
        <select id="categoryFilter" v-model="selectedCategory">
          <option value="all">All</option>
          <option value="business">Business</option>
          <option value="personal">Personal</option>
        </select>
      </div>



      <div class="list">
        <div v-for="todo in filteredTodos" :class="`todo-item ${todo.done && 'done'}`" :key="todo.id">

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
            <p>Created on: {{ formatDate(todo.createdAt) }}</p>
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
            <button class="update" @click="updateTodoOnServer(todo)">Update</button>
          </div>

        </div>
      </div>



    </section>
  </main>
</template>

<script setup>
import { ref, onMounted, computed, watch } from 'vue'
import axios from 'axios';



const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)


const todos_asc = computed(() =>
  todos.value.slice().sort((a, b) => b.createdAt - a.createdAt)
);

const formatDate = (timestamp) => {
  const date = new Date(timestamp);
  return date.toLocaleString();
};



const selectedCategory = ref('all');

const filteredTodos = computed(() => {
  if (selectedCategory.value === 'all') {
    return todos_asc.value;
  } else {
    return todos_asc.value.filter(todo => todo.category === selectedCategory.value);
  }
});



const fetchTodosFromServer = async () => {
  try {
    const response = await axios.get('http://localhost:3000/todos');
    todos.value = response.data;
  } catch (error) {
    console.error('Error fetching todos from server:', error);
  }
};

const addTodoToServer = async (todo) => {
  try {
    const response = await axios.post('http://localhost:3000/todos', todo);
    console.log('Todo added to server:', response.data);
    return response.data;
  } catch (error) {
    console.error('Error adding todo to server:', error);
    throw error;
  }
};

const removeTodoFromServer = async (todo) => {
  try {
    if (!todo.id) {
      console.error('Invalid todo id:', todo.id);
      return;
    }

    await axios.delete(`http://localhost:3000/todos/${todo.id}`);
    console.log('Todo removed from server');
  } catch (error) {
    console.error('Error removing todo from server:', error);
    throw error;
  }
};

const updateTodoOnServer = async (todo) => {
  try {
    if (!todo.id) {
      console.error('Invalid todo id:', todo.id);
      return;
    }

    const response = await axios.put(`http://localhost:3000/todos/${todo.id}`, todo);
    console.log('Todo updated on server:', response.data);
    return response.data;
  } catch (error) {
    console.error('Error updating todo on server:', error);
    throw error;
  }
};


const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value == null) {
    return;
  }
  const newTodo = {
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  };

  addTodoToServer(newTodo).then((addedTodo) => {
    todos.value.push(addedTodo);
    input_content.value = '';
    input_category.value = null;

  });
};

const removeTodo = (todo) => {
  removeTodoFromServer(todo).then(() => {
    todos.value = todos.value.filter((t) => t !== todo);
  });
};



watch(todos, (newval) => {
  localStorage.setItem('todos', JSON.stringify(newval));
}, { deep: true });

watch(name, (newval) => {
  localStorage.setItem('name', newval)
});

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
  fetchTodosFromServer();
});

</script>