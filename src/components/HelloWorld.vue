<template>
  <div class="hello">
    <h1>{{ msg }}</h1>

    <button @click="showModal()">モーダルを表示</button>
    <modal
      :message="message"
      v-show="modal"
      @execute-method="executeMethod"
    ></modal>
  </div>
</template>

<script>
import Modal from "./Modal.vue";

export default {
  name: "HelloWorld",
  components: {
    Modal,
  },
  data() {
    return {
      message: "よろしいですか？",
      modal: false,
    };
  },
  props: {
    msg: String,
  },
  methods: {
    showModal() {
      // モーダル表示する際の処理が必要ならここに書く
      this.modal = true;
    },
    executeMethod(yes) {
      // モーダルを非表示にして、モーダルでの選択結果によって処理を変える
      this.modal = false;
      if (yes) {
        alert("はい が押されました。");
      } else {
        alert("いいえ が押されました。");
      }
    },
  },
};
</script>