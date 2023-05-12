<script lang="ts">
import { defineComponent, type PropType } from 'vue';
import type { TTodosArr } from './types/interface';
import StatusFilters from './components/StatusFilters.vue';
import { todos as oldTodos } from './data/todos.js'

export default defineComponent({
  name: "App",
  props: {
    todos: {
      type: Object as PropType<TTodosArr[]>,
      required: true
    },
  },
  components: {
    StatusFilters,
  },
  data() {
    let todos: TTodosArr[] = []
    const jsonData = localStorage.getItem('todos') || '[]'
    try {
      const newTodos = JSON.parse(jsonData)
      todos = !!newTodos.length ? newTodos : oldTodos
    } catch (e) {}

    return {
      todos,
      title: '',
      status: 'all',
    }
  },
  computed: {
    activeTodos() {
      return this.todos.filter(todo => !todo.completed)
    }
  },
  watch: {
    todos: {
      deep: true,
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos))
      }
    }
  },
  methods: {
    handleSubmit() {
      this.todos.push({
        id: this.todos.length + 1,
        title: this.title,
        completed: false,
      });

      this.title = ''
    }
  }
})
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos {{ todos.length }}</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button class="todoapp__toggle-all" :class="{ active: activeTodos.length > 0 }"></button>

        <form @submit.prevent="handleSubmit">
          <input
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
          />
        </form>
      </header>

      <section class="todoapp__main">
        <div
          class="todo"
          :class="{ completed: todo.completed }"
          v-for="todo, index of todos"
          :key="todo.id"
        >
          <label class="todo__status-label">
            <input
              type="checkbox"
              class="todo__status"
              v-model="todo.completed"
            />
          </label>

          <form v-if="false">
            <input
              type="text"
              class="todo__title-field"
              placeholder="Empty todo will be deleted"
              value="Todo is being edited now"
            />
          </form>

          <template v-else="true">
            <span class="todo__title">{{ todo.title }}</span>
            <button class="todo__remove" @click="todos.splice(index, 1)">x</button>
          </template>

          <div
            class="modal overlay"
            :class="{ 'is-active': false }"
          >
            <div class="modal-background has-background-white-ter"></div>
            <div class="loader"></div>
          </div>
        </div>
      </section>

      <footer class="todoapp__footer">
        <span class="todo-count">
          {{ activeTodos.length }} items left
        </span>
        <StatusFilters v-model="status" />

        <button class="todoapp__clear-completed" v-if="activeTodos.length > 0">
          Clear completed
        </button>
      </footer>
    </div>

    <article class="message is-danger message--hidden">
      <div class="message-header">
        <p>Error</p>
        <button class="delete"></button>
      </div>

      <div class="message-body">
        Unable to add a Todo
      </div>
    </article>
  </div>
</template>