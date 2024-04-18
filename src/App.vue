<template>
  <div id="app">
    <h1>ToDo List</h1>
    <ul>
      <li v-for="todoError in todoErrors" :key="todoError" class="errorDescription">
        {{ todoError }}
      </li>
    </ul>
    <todo-input @add-todo="addTodo" ref="todoInput" />
    <todo-list
      :todos="todos"
      :originalTodos="originalTodos"
      @toggle-completed="toggleCompleted"
      @edit-todo="editTodo"
      @delete-todo="deleteTodo"
      @save-edit="saveEdit"
      @sort-todos="sortTodos"
    />
  </div>
</template>



<script>
import TodoInput from './components/TodoInput.vue';
import TodoList from './components/TodoList.vue';

export default {
  components: {
    TodoInput,
    TodoList
  },
  data() {
    return {
      newTodo: '',
      newTodoDueDate: '',
      todos: [],
      originalTodos: [],
      todoErrors: [],
      sortKey: 'default' // ソートキーの初期値
    }
  },
  created() {
    this.loadTodos();
  },
  methods: {
    loadTodos() {
      const todos = localStorage.getItem('todos');
      if (todos) {
        this.todos = JSON.parse(todos);
        this.originalTodos = JSON.parse(todos);  // ロード時にオリジナルのリストを保存
      }
    },
    saveTodos() {
      localStorage.setItem('todos', JSON.stringify(this.todos));
      this.originalTodos = [...this.todos];  // 保存時にもオリジナルを更新
    },
    addTodo(newTodo) {
      this.todoErrors = [];
      if (!newTodo.text.trim()) {
        this.todoErrors.push('Todoを入力してください');
      }
      if (!newTodo.dueDate) {
        this.todoErrors.push('期限を入力してください');
      }
      if (this.todoErrors.length === 0) {
        this.todos.push({
          id: this.todos.length + 1,
          text: newTodo.text,
          dueDate: newTodo.dueDate,
          completed: false,
          editing: false,
          buttonText: '完了'
        });
        this.saveTodos();
        this.$refs.todoInput.resetInput(); 
      }
    },
    toggleCompleted(todoId) {
      const todo = this.todos.find(todo => todo.id === todoId);
      if (todo) {
        const updatedTodo = { ...todo, completed: !todo.completed };
        this.updateTodo(updatedTodo);
      }
    },
    deleteTodo(todoId) {
      // IDを基にして正確なインデックスを見つける
      const index = this.todos.findIndex(todo => todo.id === todoId);
      if (index !== -1) {
        // 確認ダイアログを表示
        if (window.confirm("本当に削除しますか？")) {
          // 配列から正確な位置のTodoを削除
          this.todos.splice(index, 1);
          this.saveTodos();
        }
      }
    },
    editTodo(todoId) {
      const todo = this.todos.find(todo => todo.id === todoId);
      if (todo) {
        const updatedTodo = { ...todo, editing: true };
        this.updateTodo(updatedTodo);
      }
    },
    saveEdit(updatedTodo) {
      const editedTodo = { ...updatedTodo, editing: false };
      this.updateTodo(editedTodo);
    },
    isPastDue(dueDate) {
      const today = new Date();
      today.setHours(0, 0, 0, 0); // 時間をリセットして今日の日付のみにする
      const due = new Date(dueDate);
      return due < today; //期限が今日より前であればtrueを返す
    },
    sortTodos(sortKey) {
      this.sortKey = sortKey // 受け取った sortKey を保存
      if (this.sortKey === 'default') {
        this.todos = [...this.originalTodos]
      } else {
        let sortedTodos = [...this.originalTodos]
        if (this.sortKey === 'completed') {
          sortedTodos.sort((a, b) => (a.completed === b.completed ? 0 : a.completed ? 1 : -1))
        } else if (this.sortKey === 'dueDate') {
          sortedTodos.sort((a, b) => new Date(a.dueDate) - new Date(b.dueDate))
        }
        this.todos = sortedTodos
      }
    },
    updateTodo(updatedTodo) {
      const index = this.todos.findIndex(todo => todo.id === updatedTodo.id);
      if (index !== -1) {
        this.todos.splice(index, 1, updatedTodo);
        this.saveTodos();
      }
    },
  }
}
</script>

<style>
#app {
  max-width: 600px;
  margin: 0 auto;
  font-family: 'Helvetica Neue', Arial, sans-serif;
}

.input-area {
  display: flex;
  margin-bottom: 20px;
}

input[type="text"], input[type="date"] {
  flex: 1;
  padding: 10px;
  border: 2px solid #ccc;
  border-radius: 4px;
  margin-right: 5px;
}

/* フォーカス時のスタイル */
input[type="date"]:focus {
  outline: none;
  border-color: #4CAF50;
}

button {
  background-color: #4CAF50;
  border: none;
  color: white;
  padding: 10px 15px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  cursor: pointer;
  border-radius: 4px;
  margin-left: 5px;
}

.todo-container {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.todo-content {
  display: flex;
  flex-direction: column;
}

.due-date {
  color: #666;
  font-size: 0.9em;
}

.buttons {
  display: flex;
  align-items: center;
}

button {
  background-color: #4CAF50; /* 緑色のボタン */
  border: none; /* 枠線なし */
  color: white; /* テキスト色 */
  padding: 10px 15px; /* パディング */
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  transition-duration: 0.4s; /* ホバー効果のためのトランジション */
  cursor: pointer; /* マウスカーソルを指に */
  border-radius: 4px; /* 角を丸く */
}

button:hover {
  background-color: #45a049; /* ホバー時に少し暗く */
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  margin: 8px 0;
  background-color: #f3f3f3;
  padding: 10px;
  border-radius: 4px;
}

.pastDue {
  background-color: rgb(237, 176, 176);
}

.errorDescription {
  color: red;
}
</style>