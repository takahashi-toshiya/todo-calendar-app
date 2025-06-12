<template>
  <div
    class="task-item"
    draggable="true"
    @dragstart="onDragStart"
    @dragend="onDragEnd"
  >
    {{ task.title }}
  </div>
</template>

<script lang="ts">
import { defineComponent, PropType } from "vue";
import type { Task } from "../App.vue";

export default defineComponent({
  name: "TaskItem",
  props: {
    task: {
      type: Object as PropType<Task>,
      required: true,
    },
  },
  emits: ["drag-start", "drag-end"],
  setup(props, { emit }) {
    const onDragStart = (event: DragEvent) => {
      event.dataTransfer?.setData("text/plain", props.task.id.toString());
      emit("drag-start", props.task.id);
    };

    const onDragEnd = () => {
      emit("drag-end");
    };

    return {
      onDragStart,
      onDragEnd,
    };
  },
});
</script>

<style scoped>
.task-item {
  padding: 0.5rem;
  margin: 0.25rem 0;
  background-color: #f0f9f4;
  border: 1px solid #42b983;
  border-radius: 4px;
  cursor: grab;
  user-select: none;
}

.task-item:active {
  cursor: grabbing;
  opacity: 0.7;
}
</style>
