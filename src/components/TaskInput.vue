<template>
  <form @submit.prevent="onSubmit" class="task-input">
    <input
      v-model="title"
      type="text"
      placeholder="予定を入力してください"
      required
      maxlength="100"
    />
    <input v-model="date" type="date" required />
    <button type="submit">追加</button>
  </form>
</template>

<script lang="ts">
import { defineComponent, ref, PropType } from "vue";
import type { Task } from "../App.vue";

export default defineComponent({
  name: "TaskInput",
  props: {
    tasks: {
      type: Array as PropType<Task[]>,
      required: true,
    },
  },
  emits: ["add-task"],
  setup(props, { emit }) {
    const title = ref("");
    const date = ref(new Date().toISOString().substr(0, 10));

    const onSubmit = () => {
      // 重複チェックは親で行うが、ここでも簡易チェック可能
      if (
        props.tasks.some(
          (t) => t.title === title.value && t.date === date.value
        )
      ) {
        alert("同じ日付に同じ予定が既に存在します。");
        return;
      }
      emit("add-task", { title: title.value, date: date.value });
      title.value = "";
    };

    return {
      title,
      date,
      onSubmit,
    };
  },
});
</script>

<style scoped>
.task-input {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

input[type="text"] {
  flex: 1;
  padding: 0.5rem;
  font-size: 1rem;
}

input[type="date"] {
  padding: 0.5rem;
  font-size: 1rem;
}

button {
  padding: 0.5rem 1rem;
  background-color: #42b983;
  border: none;
  color: white;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #369870;
}
</style>
