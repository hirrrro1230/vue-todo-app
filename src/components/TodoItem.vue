<template>
    <li :class="{ 'completed': todo.completed, 'pastDue': isPastDue(todo.dueDate), 'editing': todo.editing }">
      <div class="todo-container" v-if="todo.editing">
        <input type="text" v-model="localTodo.text">
        <input type="date" v-model="localTodo.dueDate">
        <button @click="saveEdit">保存</button>
      </div>
      <div class="todo-content" v-else>
        <span>{{ todo.text }}</span>
        <span class="due-date">- 期限: {{ todo.dueDate }}</span>
        <div class="buttons">
          <button :class="{ 'completeButton': !todo.completed, 'completed': todo.completed }" @click="$emit('toggle-completed', todo.id)">
            {{ todo.completed ? '未完了に戻す' : '完了' }}
          </button>
          <button @click="$emit('edit-todo', todo.id)">編集</button>
          <button @click="$emit('delete-todo', todo.id)" class="deleteButton">削除</button>
        </div>
      </div>
    </li>
</template>

  
  <script>
  export default {
    props: ['todo'],
    data() {
      return {
        localTodo: {...this.todo}  // ローカルの変更可能なコピーを作成
      };
    },
    methods: {
      isPastDue(dueDate) {
        const today = new Date();
        today.setHours(0, 0, 0, 0);
        return new Date(dueDate) < today;
      },
      saveEdit() {
        this.$emit('save-edit', this.localTodo);  // 更新されたtodoをemit
      }
    }
  }
  </script>
  <style scoped>
  .todo-container {
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  
  .buttons {
    display: flex;
    align-items: center;
    justify-content: flex-end; /* ボタンを右側に配置 */
  }
  
  button {
    margin-left: 10px;
  }
  
  .completed {
    background-color: #5e626f; /* 完了時のボタンの背景色 */
  }

  .completeButton {
    background-color: rgb(24, 130, 243); /* 青色のボタン */
    color: white; /* テキスト色 */
    padding: 10px 15px; /* パディング */
    border: none; /* 枠線なし */
    border-radius: 4px; /* 角を丸く */
    cursor: pointer; /* マウスカーソルを指に */
    font-size: 16px;
  }
  
  .completeButton:hover {
    background-color: #1a82e2; /* ホバー時に少し暗くする */
  }
  
  .completed {
    background-color: #d4edda; /* 完了項目の背景色 */
    color: #155724; /* 完了項目のテキスト色 */
  }
  
  .editButton, .deleteButton {
    background-color: #4CAF50; /* 緑色のボタン */
    color: white; /* テキスト色 */
  }
  
  .deleteButton {
    background-color: rgb(243, 24, 24); /* 赤色のボタン */
  }
  
  .editButton:hover, .deleteButton:hover {
    opacity: 0.85; /* ホバー時に少し透明にする */
  }
  </style>