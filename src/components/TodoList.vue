<template>
  <div>
    <div class="name-container">
      Welcome, {{ name }}
    </div>
    <input type="text" class="todo-input" placeholder="What needs to be done" v-model="newTodo" @keyup.enter="addTodo">
    <div v-if="$store.state.loading" class="lds-ring"><div></div><div></div><div></div><div></div></div>
    <transition-group name="fade" enter-active-class="animated fadeInLeft" leave-active-class="animated fadeOutRight">
      <todo-item v-for="(todo, index) in todosFiltered" :key="todo.id" :todo="todo" :index="index" :checkAll="!anyRemaining"></todo-item>
    </transition-group>

    <div class="extra-container">
      <todo-check-all></todo-check-all>
      <todo-items-remaining></todo-items-remaining>
    </div>

    <div class="extra-container">
      <todo-filtered></todo-filtered>
      <div>
        <transition name="fade">
          <todo-clear-completed></todo-clear-completed>
        </transition>
      </div>
    </div>

  </div>
</template>

<script>
import TodoItem from './TodoItem'
import TodoRemaining from "./TodoItemsRemaining"
import TodoItemsRemaining from "./TodoItemsRemaining"
import TodoCheckAll from "./TodoCheckAll"
import TodoFiltered from "./TodoFiltered"
import TodoClearCompleted from "./TodoClearCompleted"

export default {
  name: 'todo-list',
  components: {
    TodoClearCompleted,
    TodoFiltered,
    TodoCheckAll,
    TodoRemaining,
    TodoItem,
    TodoItemsRemaining,
  },
  data () {
    return {
      newTodo: '',
      idForTodo: 3,
      name: '',
    }
  },
  created() {
    this.$store.dispatch('retrieveTodos')
    this.$store.dispatch('retrieveName')
    .then(response => {
      this.name = response.data.name
    })
  },
  computed: {
    anyRemaining() {
      return this.$store.getters.anyRemaining
    },
    todosFiltered() {
      return this.$store.getters.todosFiltered
    },
  },
  methods: {
    addTodo() {
      if(this.newTodo.trim().length === 0) {
        return
      }
      this.$store.dispatch('addTodo', {
        id: this.idForTodo,
        title: this.newTodo,
      })

      this.newTodo = ''
      this.idForTodo++
    },
  }
}
</script>
<style>
  .name-container {
    margin-bottom: 16px;
  }
  .todo-input {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
  }
  .todo-input:focus {
    outline: 0;
  }
  .todo-item {
    margin-bottom: 12px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    animation-duration: .3s;
  }
  .remove-item {
    cursor: pointer;
    margin-left: 14px;
  }
  .remove-item:hover {
    color: black;
  }
  .todo-item-left {
    display: flex;
    align-items: center;
  }
  .todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
  }
  .todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
  }
  .todo-item-edit:focus {
    outline: 0;
  }
  .completed {
    text-decoration: line-through;
    color: gray;
  }
  .extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgray;
    padding-top: 14px;
    margin-bottom: 14px;
  }
  button {
    font-size: 14px;
    background-color: white;
    appearance: none;
    border-top: 1px solid lightgray;
    padding: 4px 8px;
  }
  button:hover {
    background-color: lightgray;
  }
  button:focus {
    outline: none;
  }
  .active {
    background-color: lightgreen;
  }
  .fade-enter-active, .fade-leave-active {
    transition: opacity .2s;
  }
  .fade-enter, .fade-leave-to {
    opacity: 0;
  }
</style>
