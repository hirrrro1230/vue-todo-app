<template>
  <div class="input-area">
    <input type="text" v-model="newTodo" @keyup.enter="addTodo">
    <input type="date" v-model="newTodoDueDate">
    <button @click="addTodo">追加</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTodo: '',
      newTodoDueDate: ''
    };
  },
  methods: {
    addTodo() {
      this.$emit('add-todo', {
        text: this.newTodo,
        dueDate: this.newTodoDueDate
      });
    },
    resetInput() {
      this.newTodo = '';
      this.newTodoDueDate = '';
    }
  }
}
</script>
