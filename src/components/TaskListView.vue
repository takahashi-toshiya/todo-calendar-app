<template>
  <div class="task-list-view">
    <div
      v-for="date in sortedDates"
      :key="date"
      class="date-group"
      @dragover.prevent
      @drop="onDrop(date)"
    >
      <h3>{{ date }}</h3>
      <TaskItem
        v-for="task in tasksByDate(date)"
        :key="task.id"
        :task="task"
        @drag-start="onDragStart"
        @drag-end="onDragEnd"
      />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, PropType } from "vue";
import TaskItem from "./TaskItem.vue";
import type { Task } from "../App.vue";

export default defineComponent({
  name: "TaskListView",
  components: {
    TaskItem,
  },
  props: {
    tasks: {
      type: Array as PropType<Task[]>,
      required: true,
    },
  },
  emits: ["update-task-date"],
  setup(props, { emit }) {
    const draggedTaskId = ref<number | null>(null);

    const uniqueDates = computed(() => {
      const dates = props.tasks.map((t) => t.date);
      return Array.from(new Set(dates));
    });

    const sortedDates = computed(() => {
      return uniqueDates.value.sort();
    });

    const tasksByDate = (date: string) => {
      return props.tasks.filter((t) => t.date === date);
    };

    const onDragStart = (taskId: number) => {
      draggedTaskId.value = taskId;
    };

    const onDragEnd = () => {
      draggedTaskId.value = null;
    };

    const onDrop = (date: string) => {
      if (draggedTaskId.value !== null) {
        emit("update-task-date", draggedTaskId.value, date);
        draggedTaskId.value = null;
      }
    };

    return {
      sortedDates,
      tasksByDate,
      onDragStart,
      onDragEnd,
      onDrop,
    };
  },
});
</script>

<style scoped>
.task-list-view {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.date-group {
  border: 1px solid #ddd;
  padding: 0.5rem;
  border-radius: 4px;
  background-color: #f9f9f9;
}

.date-group h3 {
  margin: 0 0 0.5rem 0;
}
</style>
