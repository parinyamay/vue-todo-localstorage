<script setup lang="ts">
  import { ref, onMounted, computed, watch } from 'vue'

  interface TodoItems {
    content: string,
    category: string,
    done: boolean,
    createdAt: number,
  }

  const todos = ref<TodoItems[]>([])
  const name = ref<string>('')

  const inputContent = ref<string>('')
  const inputCategory = ref<any>(null)

  const todosAsc = computed( () => todos.value.sort((a, b) => {
      return b.createdAt - a.createdAt
    })
  )

  // Set value from input on submit button
  const addTodo = () => {
    if (inputContent.value.trim() === '' || inputCategory.value === '') return
    
    todos.value.push({
      content: inputContent.value,
      category: inputCategory.value,
      done: false,
      createdAt: Date.now(),
    })


    inputContent.value = ''
    inputCategory.value = null
  }

  const removeTodo = (todo: TodoItems) => {
    todos.value = todos.value.filter(t => t !== todo)
  }

  // Set todos items to localStorage on submit action
  watch(todos, newValue => {
    localStorage.setItem('todos', JSON.stringify(newValue))
  },{ deep: true })

  // Set value to localStorage on input
  watch(name, newValue => {
    localStorage.setItem('name', newValue)
  })

  // Get name value on mounted
  onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos') || '[]')
  })

</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">What's up,
        <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          placeholder="e.g. make a video"
          v-model="inputContent"
        />

        <h4>Pick as category</h4>
        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="inputCategory"  
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="personal1"
              value="personal"
              v-model="inputCategory"  
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">
        <div
          v-for="(todo, index) in todosAsc"
          :key="index"
          :class="`todo-item ${todo.done && 'done'}`"
        >
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          
          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">
              Delete
            </button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
