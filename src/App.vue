<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_content.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category,
    createdAt: new Date().getTime(),
    done: false
  })
  input_content.value=null
  input_category.value=''

}

const removeTodo= todo=>{
  todos.value=todos.value.filter(t => t !== todo)
}

/*  t represents each individual todo item in the array as the filter method iterates through it.

t !== todo is the condition being checked for each element t in the array. It checks if the current todo item t is not equal (!==) to the todo item that you want to remove.

If t !== todo evaluates to true, it means the current todo item t is not the same as the one you want to remove, and it will be included in the new filtered array.

If t !== todo evaluates to false, it means the current todo item t is the same as the one you want to remove, and it will be excluded from the new filtered array.

After the filtering process is complete, todos.value is assigned the new filtered array.   */

watch(todos, newValue => {
  localStorage.setItem('todos', JSON.stringify(newValue))
}, { deep: true })


watch(name, (newValue) => {
  localStorage.setItem('name', newValue)
})
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos') || '[]')
})



</script>

<template>
  <main class="app">

    <section class="greeting">

      <h2 class="title">What's up , <input type="text" placeholder="Name here" v-model="name"></h2>

    </section>


    <section class="create-todo">
      <h3>CREATE A TODO LIST</h3>

      <form @submit.prevent="addTodo">

        <h4>What's on your todo list ?</h4>
        <input type="text" placeholder="e.g make a video ..etc" v-model="input_content">

        <!-- {{ input_content }} -->
        <h4>Pick a category </h4>
        <div class="options">

          <label>
            <input type="radio" name="category" value="business" v-model="input_category">

            <span class="bubble business"></span>
            <div>Business</div>

          </label>


          <label>
            <input type="radio" name="category" value="personal" v-model="input_category">

            <span class="bubble personal"></span>
            <div>Personal</div>

          </label>

        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>


    <section class="todo-list">
      <h3>Your list is :</h3>
      <div class="list">

        <div v-for="todo in todos_asc" :class=" `todo-item ${todo.done && 'done'}`">
           <label>
            <input type="checkbox" v-model="todo.done"/>
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

