<template>
  <div class="calendar-week-view">
    <div class="week-days">
      <div
        v-for="day in weekDays"
        :key="day"
        class="day-column"
        @dragover.prevent
        @drop="onDrop(day)"
      >
        <div class="day-label">{{ day }}</div>
        <div class="tasks">
          <TaskItem
            v-for="task in tasksByDate(day)"
            :key="task.id"
            :task="task"
            @drag-start="onDragStart"
            @drag-end="onDragEnd"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed, PropType } from "vue";
import TaskItem from "./TaskItem.vue";
import type { Task } from "../App.vue";

export default defineComponent({
  name: "CalendarWeekView",
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

    // Get current week days as yyyy-mm-dd strings (Sunday to Saturday)
    const weekDays = computed(() => {
      const today = new Date();
      const dayOfWeek = today.getDay(); // 0 (Sun) - 6 (Sat)
      const sunday = new Date(today);
      sunday.setDate(today.getDate() - dayOfWeek);
      const days = [];
      for (let i = 0; i < 7; i++) {
        const d = new Date(sunday);
        d.setDate(sunday.getDate() + i);
        days.push(d.toISOString().substr(0, 10));
      }
      return days;
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
      weekDays,
      tasksByDate,
      onDragStart,
      onDragEnd,
      onDrop,
    };
  },
});
</script>

<style scoped>
.calendar-week-view {
  display: flex;
  flex-direction: column;
}

.week-days {
  display: flex;
  justify-content: space-between;
}

.day-column {
  flex: 1;
  border: 1px solid #ddd;
  margin: 0 2px;
  padding: 0.5rem;
  min-height: 150px;
  background-color: #f9f9f9;
  border-radius: 4px;
}

.day-label {
  font-weight: bold;
  margin-bottom: 0.5rem;
  text-align: center;
}

.tasks {
  min-height: 100px;
}
</style>
