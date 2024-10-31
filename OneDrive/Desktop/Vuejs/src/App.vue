<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

//on submit
const addTodo = () => {
  //input_content.value.trim() === '' causes insertion error
  if(input_content.value === '' || input_category.value === ''){
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
  input_content.value = ''
  input_category.value = null
}

//set storage
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
},{ deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

const removeTodo = (todo) =>{
  todos.value = todos.value.filter(t => t !== todo)
}

//on submit 
onMounted(()=>{
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title"> 
        Hi Shana
      </h2>
    </section>

    <section>
      <h3>Todo</h3>
      <!--<form action="" @submit.prevent="addTodo">-->
      <form @submit.prevent="addTodo" class="create-todo">
        <h4>Items</h4>
        <img src="./assets/unnamed82.png" alt="">
        <img src="./assets/unnamed84.png" alt=""> 
        <br>
        <label for="">Input</label>
        <input type="text" placeholder="name" v-model="input_content"/>

        <h4>Category</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category"/>
            <span class="bubble business"></span>
            <div>business</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>personal</div>
          </label>

        </div>
        <input type="submit" value="Add"/>
      </form>
    </section>

    <section class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
        <div 
          v-for="todo in todos_asc" 
          :key="todo.createdAt" 
          :class="['todo-item', todo.category]" 
          >
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content"/>
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>

  </main>

</template>
