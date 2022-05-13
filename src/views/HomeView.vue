<script lang="ts">
import { defineComponent } from 'vue'
// import TheWelcome from '@/components/TheWelcome.vue'

const STORAGE_KEY = 'todo_list';

interface ObjTodo {
  completed: Boolean
  id: String
  title: String
}

const filters: any = {
  all: (todos:Array<ObjTodo>) => todos,
  active: (todos:Array<ObjTodo>) => todos.filter((todo) => !todo.completed),
  completed: (todos:Array<ObjTodo>) => todos.filter((todo) => todo.completed)
}

export default defineComponent({
  data: () => {
    return {
      todos: JSON.parse(localStorage.getItem(STORAGE_KEY) || '[]'),
      editedTodo: null,
      visibility: 'all'
    }
  },
  methods: {
    addTodo(e: any) {
      const value = e.target.value.trim();
      
      if (!value) {
        return;
      }

      this.todos.push({
        id: Date.now(),
        title: value,
        completed: false
      }),
      e.target.value = '';
    }
  },
  computed: {
    filteredTodos() {
      return filters[this.visibility](this.todos)
    },
    remaining() {
      return filters.active(this.todos).length
    },
    removeTodo(todo:ObjTodo){
      return this.todos.splice(this.todos.indexOf(todo), 1)
    },
  },

})
</script>

<template>
  <main>
    <h2 class="wrapper">
      <div class="title">TODO LIST</div>
      <input 
      autofocus 
      placeholder="Thêm công việc của bạn" 
      @keyup.enter="addTodo"
      />
      <section class="main" v-show="todos.length">
          <input
            id="toggle-all"
            class="toggle-all"
            type="checkbox"
          >

<ul class="todo-list">
        <li
          v-for="todo in filteredTodos"
          class="todo"
          :key="todo.id"
          :class="{ completed: todo.completed, editing: todo === editedTodo }"
        >
          <div class="view">
            <input class="toggle" type="checkbox" v-model="todo.completed">
            <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
            <button class="destroy" @click="removeTodo(todo)"></button>
          </div>
          <input
            v-if="todo === editedTodo"
            class="edit"
            type="text"
            v-model="todo.title"
          >
        </li>
      </ul>
      </section>
    </h2>
  </main>
</template>

<style>
  @import "https://unpkg.com/todomvc-app-css@2.4.1/index.css";
</style>
