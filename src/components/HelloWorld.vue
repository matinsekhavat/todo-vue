<script setup lang="ts">
import { computed, ref } from "vue";
const todos = ref<Array<{ id: number; title: string; isComplete: boolean }>>(
  []
);

const todoTitle = ref("");
const selectedEditableTodo = ref<null | number>(null);
const editTitle = ref(""); // Add a separate ref for editing

// Find the current todo being edited
const currentEditTodo = computed(() => {
  if (selectedEditableTodo.value === null) return null;
  return todos.value.find((item) => item.id === selectedEditableTodo.value);
});

const totalTodos = computed(() => todos.value?.length || 0);

const addHandler = () => {
  if (todoTitle.value.trim()) {
    const numericId = Date.now();
    todos.value.push({
      id: numericId,
      isComplete: false,
      title: todoTitle.value,
    });
    todoTitle.value = "";
  }
};

const handleToggleStatus = (id: number) => {
  const selectedTodo = todos.value.find((item) => item.id === id);
  if (selectedTodo) {
    selectedTodo.isComplete = !selectedTodo.isComplete;
  }
};

const startEdit = (id: number) => {
  selectedEditableTodo.value = id;
  const todoToEdit = todos.value.find((item) => item.id === id);
  if (todoToEdit) {
    editTitle.value = todoToEdit.title; // Set the edit title when starting edit
  }
};

const handleEditSubmit = () => {
  if (!selectedEditableTodo.value) return;

  const selectedTodo = todos.value.find(
    (item) => item.id === selectedEditableTodo.value
  );
  if (selectedTodo && editTitle.value.trim()) {
    selectedTodo.title = editTitle.value;
    selectedEditableTodo.value = null;
    editTitle.value = "";
  }
};

const cancelEdit = () => {
  selectedEditableTodo.value = null;
  editTitle.value = "";
};

const handleDeleteTodo = (id: number) => {
  todos.value = todos.value.filter((item) => item.id !== id);
};
</script>

<template>
  <div class="min-h-screen bg-gradient-to-br from-emerald-50 to-teal-50 py-8">
    <div class="max-w-3xl mx-auto p-6 bg-white rounded-xl shadow-lg">
      <!-- HEADER -->
      <div
        class="flex items-center justify-between mb-6 border-b pb-4 border-emerald-100"
      >
        <h1 class="text-2xl font-bold text-emerald-600">Vue.js - Todo</h1>
        <p
          class="bg-emerald-100 text-emerald-800 px-3 py-1 rounded-full text-sm font-medium"
        >
          All tasks: {{ totalTodos }}
        </p>
      </div>

      <!-- Edit mode indicator -->
      <div
        v-if="selectedEditableTodo !== null"
        class="mb-2 text-sm text-emerald-600"
      >
        Editing todo: {{ currentEditTodo?.title }}
      </div>

      <!-- Form for adding/editing todos -->
      <form
        @submit.prevent="
          selectedEditableTodo !== null ? handleEditSubmit() : addHandler()
        "
        class="flex items-center gap-4 mb-8"
      >
        <input
          v-if="selectedEditableTodo === null"
          v-model="todoTitle"
          placeholder="Add a new todo..."
          class="flex-1 border border-emerald-200 px-4 py-2 rounded-lg focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-transparent"
        />
        <input
          v-else
          v-model="editTitle"
          placeholder="Edit todo..."
          class="flex-1 border border-emerald-200 px-4 py-2 rounded-lg focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:border-transparent"
        />
        <button
          type="submit"
          class="bg-emerald-500 hover:bg-emerald-600 text-white px-4 py-2 rounded-lg transition-colors duration-300 shadow-sm"
        >
          {{ selectedEditableTodo !== null ? "Update" : "Add Todo" }}
        </button>
        <button
          v-if="selectedEditableTodo !== null"
          type="button"
          @click="cancelEdit"
          class="bg-gray-300 hover:bg-gray-400 text-gray-800 px-4 py-2 rounded-lg transition-colors duration-300 shadow-sm"
        >
          Cancel
        </button>
      </form>

      <div class="bg-emerald-50 p-4 rounded-lg">
        <h2 class="text-lg font-medium text-emerald-700 mb-3">Your Todos</h2>
        <ul v-if="!!totalTodos" class="space-y-2">
          <li
            v-for="list of todos"
            :key="list.id"
            class="flex items-center p-3 bg-white rounded-md shadow-sm hover:shadow-md transition-shadow duration-300 border-l-4 border-emerald-400"
          >
            <span class="h-4 w-4 rounded-full bg-emerald-200 mr-3"></span>
            <span
              :class="[
                'flex-1 text-gray-700',
                list.isComplete ? 'line-through' : '',
              ]"
            >
              {{ list.title }}
            </span>
            <div class="flex items-center gap-2">
              <button
                class="text-emerald-500 hover:text-emerald-700 cursor-pointer"
                @click="handleToggleStatus(list.id)"
              >
                <span class="sr-only">Complete</span>
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="h-5 w-5"
                  viewBox="0 0 20 20"
                  fill="currentColor"
                >
                  <path
                    fill-rule="evenodd"
                    d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z"
                    clip-rule="evenodd"
                  />
                </svg>
              </button>
              <button
                @click="startEdit(list.id)"
                class="text-blue-500 hover:text-blue-700"
              >
                Edit
              </button>
              <button @click="handleDeleteTodo(list.id)">Delete</button>
            </div>
          </li>
        </ul>
        <p v-else class="text-center py-6 text-gray-500 italic">
          There are no todos yet
        </p>
      </div>

      <div class="mt-6 text-center text-sm text-gray-500">
        <p>Made with Vue.js and Tailwind CSS</p>
      </div>
    </div>
  </div>
</template>
