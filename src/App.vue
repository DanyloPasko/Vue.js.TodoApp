<script>
import Filter from './components/Filter.vue'
import Item from './components/Item.vue'
export default {
  components: {
    Filter,
    Item
  },
  data() {
    let todos = [];
    const jsonData = localStorage.getItem('todos') || '[]';
    try {
      todos = JSON.parse(jsonData);
    } catch (error) { };

    return {
      todos,
      title: '',
      status: 'all',
    };
  },
  computed: {
    activeTodos() {
      return this.todos.filter(todo => !todo.completed);
    },
    completedTodos() {
      return this.todos.filter(todo => todo.completed);
    },
    visibleTodos() {
      switch (this.status) {
        case 'active':
          return this.activeTodos;
        case 'completed':
          return this.completedTodos;

        default:
          return this.todos;
      }
    }
  },
  watch: {
    todos: {
      deep: true,
      handler() {
        localStorage.setItem('todos', JSON.stringify(this.todos));
      },
    }
  },
  methods: {
    handleSubmit() {
      this.todos.push({
        id: Date.now(),
        title: this.title,
        completed: false,
      })

      this.title = '';
    },
    clearCompleted() {
      this.todos = this.activeTodos;
    }
  }
};
</script>

<template>
  <div class="todoapp">
    <h1 class="todoapp__title">todos</h1>

    <div class="todoapp__content">
      <header class="todoapp__header">
        <button
          type="button"
          class="todoapp__toggle-all"
          :class="{ active: activeTodos.length === 0 }"
        >
        </button>

        <form @submit.prevent="handleSubmit">
          <input
            type="text"
            class="todoapp__new-todo"
            placeholder="What needs to be done?"
            v-model="title"
          />
        </form>
      </header>

      <TransitionGroup
        name="list"
        tag="section"
        class="todoapp__main"
      >
        <Item
          v-for="todo, index of visibleTodos"
          :key="todo.id"
          :todo="todo"
          @update="Object.assign(todo, $event)"
          @delete="todos.splice(todos.indexOf(todo), 1)"
        />

      </TransitionGroup>

      <footer class="todoapp__footer">
        <span class="todo-count"> {{ activeTodos.length }} items left </span>
        <Filter v-model="status" />
        <button
          type="button"
          class="todoapp__clear-completed"
          v-if="completedTodos.length > 0"
          @click="clearCompleted"
        >
          Clear completed
        </button>
      </footer>
    </div>

    <article class="message is-danger message--hidden">
      <div class="message-header">
        <p>Error</p>
        <button class="delete"></button>
      </div>

      <div class="message-body">Unable to add todo</div>
    </article>
  </div>
</template>

<style>
.list-enter-active,
.list-leave-active {
  max-height: 60px;
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  max-height: 0px;
  transform: scaleY(0);
}
</style>