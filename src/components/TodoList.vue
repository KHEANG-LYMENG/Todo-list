<template>
  <div class="max-w-md mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Todo List</h1>
    
    <!-- Input field -->
    <div class="mb-4 flex gap-2">
      <input type="text" v-model="newTodo" @keydown="handleKeydown" placeholder="Add a new todo" class="flex-1 p-2 border rounded">
      <button v-if="!isEditing" @click="addTodo" class="px-4 py-2 bg-blue-500 text-white rounded hover:bg-blue-600 transition-colors">Add</button>
      <button v-else @click="updateTodo" class="px-4 py-2 bg-green-500 text-white rounded hover:bg-green-600 transition-colors">Update</button>
    </div>

    <!-- Todo list -->
    <ul class="space-y-2">
      <li v-for="todo in todos" :key="todo.id" class="flex items-center justify-between p-2 border rounded group hover:border-gray-400 transition-colors">
        <span>{{ todo.text }}</span>
        <div class="invisible group-hover:visible flex gap-2">
          <button
            @click="startEditing(todo)" class="px-3 py-1 text-sm bg-gray-100 rounded hover:bg-gray-200 transition-colors">Edit</button>
          <button
            @click="removeTodo(todo.id)" class="px-3 py-1 text-sm text-white bg-red-500 rounded hover:bg-red-600 transition-colors">Remove</button>
        </div>
      </li>
    </ul>
  </div>
</template>


<script setup>
  import { ref } from 'vue'

  const todos = ref([])
  const newTodo = ref('')
  const editingTodo = ref(null)
  const isEditing = ref(false)

  // Add todo
  const addTodo = () => {
    const trimmedText = newTodo.value.trim()
    // alert for empty text
    if (!trimmedText) {
      alert("Todo input cannot be empty!");
      return;
    }

    // Check for duplicates
    if (todos.value.some(todo => todo.text === trimmedText)) {
      alert('This todo already exists!')
      return
    }

    todos.value.push({
      id: Date.now(),
      text: trimmedText
    })
    newTodo.value = ''
  }

  // remove todo
  const removeTodo = (id) => {
    // confirmation on remove
    if (confirm("Are you sure you want to remove this todo?")) {
      todos.value = todos.value.filter(todo => todo.id !== id)
    }
  }

  // editing
  const startEditing = (todo) => {
    editingTodo.value = todo
    newTodo.value = todo.text
    isEditing.value = true
  }

  //update todo
  const updateTodo = () => {
    const trimmedText = newTodo.value.trim()
    if (!trimmedText) {
      return
    }

    // Check for duplicates excluding the current todo
    const hasDuplicate = todos.value.some(t => 
      t.id !== editingTodo.value.id && t.text === trimmedText
    )

    if (hasDuplicate) {
      alert('This todo already exists!')
      return
    }

    const todo = todos.value.find(t => t.id === editingTodo.value.id)
    if (todo) {
      todo.text = trimmedText
    }
    
    editingTodo.value = null
    newTodo.value = ''
    isEditing.value = false
  }

  const handleKeydown = (e) => {
    if (e.key === 'Enter') {
      if (isEditing.value) {
        updateTodo()
      } else {
        addTodo()
      }
    }
  }
</script>
