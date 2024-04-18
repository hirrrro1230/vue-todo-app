<template>
    <div>
      <select v-model="sortKey" @change="sortTodos">
        <option value="default">デフォルト</option>
        <option value="completed">完了順</option>
        <option value="dueDate">期限順</option>
      </select>
      <ul>
        <todo-item
          v-for="todo in todos"
          :key="todo.id"
          :todo="todo"
          @toggle-completed="toggleCompleted"
          @edit-todo="editTodo"
          @delete-todo="deleteTodo"
          @save-edit="saveEdit"
        ></todo-item>
      </ul>
    </div>
  </template>
  
  <script>
  import TodoItem from './TodoItem.vue';
  
  export default {
    components: {
      TodoItem
    },
    props: ['todos', 'originalTodos'],
    data() {
      return {
        sortKey: 'default'
      };
    },
    methods: {
      sortTodos() {
        this.$emit('sort-todos', this.sortKey);
      },
      toggleCompleted(id) {
        this.$emit('toggle-completed', id);
      },
      editTodo(id) {
        this.$emit('edit-todo', id);
      },
      deleteTodo(id) {
        this.$emit('delete-todo', id);
      },
      saveEdit(todo) {
        this.$emit('save-edit', todo);
      }
    }
  }
  </script>
  