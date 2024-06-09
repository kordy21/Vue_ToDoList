<template>
  <div class="container w-1/3 mx-auto">
    <!-- Heading of the Todo List application -->
    <h1 class="text-4xl text-black text-center my-4">Todo List</h1>

    <!-- Buttons to toggle between viewing incomplete and completed tasks -->
    <div class="flex justify-center mb-4">
      <button
        @click="displayCompleted(false)" 
        :class="!viewCompleted ? 'nav-link active' : 'nav-link'" 
      >
        Incomplete
      </button>
      <button
        @click="displayCompleted(true)" 
        :class="viewCompleted ? 'nav-link active' : 'nav-link'" 
      >
        Complete
      </button>
    </div>

    <!-- List of todo items -->
    <ul>
      <li
        v-for="item in filteredItems"
        :key="item.id"
        class="p-2 border border-gray-400 rounded-xl my-3"
      >
        <!-- Title of the todo item, styled based on completion status -->
        <div :class="['todo-title mr-2', item.completed ? 'completed-todo' : '']">
          {{ item.title }}
        </div>
        <!-- Delete button for each todo item -->
        <div class="text-center">
          <button class="bg-red-500 rounded-xl text-white py-2 px-5" @click="handleDelete(item)">
            Delete
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, onMounted, computed } from 'vue'; 
import axios from 'axios'; 

export default {
  name: 'App',
  setup() {
    const viewCompleted = ref(false);
    const todoList = ref([]);

    // Function to fetch the list of todo items from the API
    const refreshList = () => {
      axios
        .get('https://jsonplaceholder.typicode.com/todos/?userID=1') 
        .then((res) => {
          todoList.value = res.data; 
        })
        .catch((err) => console.log(err)); 
    };

    // Function to handle deleting a todo item
    const handleDelete = (item) => {
      console.log("Attempting to delete item with ID: " + item.id);
      axios
        .delete(`https://jsonplaceholder.typicode.com/todos/${item.id}`) 
        .then((res) => {
          console.log("Deleted item with ID: " + item.id);
          refreshList(); 
        })
        .catch((err) => {
          console.error("Error deleting item with ID: " + item.id, err); 
        });
    };

    // Function to toggle the view between completed and incomplete tasks
    const displayCompleted = (status) => {
      viewCompleted.value = status; 
    };

    // Computed property to filter items based on their completion status
    const filteredItems = computed(() =>
      todoList.value.filter((item) => item.completed === viewCompleted.value)
    );

    // Fetch the list of todo items when the component is mounted
    onMounted(() => {
      refreshList();
    });

    return {
      viewCompleted,
      todoList,
      handleDelete,
      displayCompleted,
      filteredItems,
    };
  },
};
</script>

<style scoped>
.nav-link {
  @apply p-3 bg-blue-200 rounded-xl; 
}
.nav-link.active {
  @apply bg-blue-500 text-white; 
}
.nav-link:first-child {
  @apply mr-3; 
}
.todo-title {
  @apply text-lg font-bold mb-3 text-center;
}
.completed-todo {
  @apply text-gray-400 line-through; 
}
</style>
