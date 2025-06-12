<template>
  <div id="app">
    <header>
      <h1>カレンダー連動ToDoアプリ</h1>
      <button @click="toggleView">
        {{ isWeekView ? "リストビューに切替" : "週表示カレンダーに切替" }}
      </button>
    </header>

    <TaskInput @add-task="addTask" :tasks="tasks" />

    <component
      :is="isWeekView ? 'CalendarWeekView' : 'TaskListView'"
      :tasks="tasks"
      @update-task-date="updateTaskDate"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue";
import CalendarWeekView from "./components/CalendarWeekView.vue";
import TaskListView from "./components/TaskListView.vue";
import TaskInput from "./components/TaskInput.vue";

export interface Task {
  id: number;
  title: string;
  date: string; // ISO date string yyyy-mm-dd
}

export default defineComponent({
  name: "App",
  components: {
    CalendarWeekView,
    TaskListView,
    TaskInput,
  },
  setup() {
    const isWeekView = ref(true);
    const tasks = ref<Task[]>([]);

    const toggleView = () => {
      isWeekView.value = !isWeekView.value;
    };

    const addTask = (newTask: Omit<Task, "id">) => {
      // 重複チェック
      const duplicate = tasks.value.find(
        (t) => t.title === newTask.title && t.date === newTask.date
      );
      if (duplicate) {
        alert("同じ日付に同じ予定が既に存在します。");
        return;
      }
      const id = tasks.value.length
        ? Math.max(...tasks.value.map((t) => t.id)) + 1
        : 1;
      tasks.value.push({ id, ...newTask });
    };

    const updateTaskDate = (taskId: number, newDate: string) => {
      const task = tasks.value.find((t) => t.id === taskId);
      if (!task) return;

      // 重複チェック
      const duplicate = tasks.value.find(
        (t) => t.title === task.title && t.date === newDate
      );
      if (duplicate) {
        alert("移動先の日付に同じ予定が既に存在します。");
        return;
      }

      task.date = newDate;
    };

    return {
      isWeekView,
      tasks,
      toggleView,
      addTask,
      updateTaskDate,
    };
  },
});
</script>

<style scoped>
#app {
  max-width: 900px;
  margin: 0 auto;
  padding: 1rem;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen,
    Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}

header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
}

button {
  cursor: pointer;
  padding: 0.5rem 1rem;
  background-color: #42b983;
  border: none;
  color: white;
  border-radius: 4px;
}

button:hover {
  background-color: #369870;
}
</style>
