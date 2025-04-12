<script lang="ts" setup>
import { ref, watch, nextTick } from 'vue';
import type { Task } from './types';

const props = defineProps<{
  tasks: Task[];
  tasksDone: Task[];
  donelist: Task[];
}>();

const emit = defineEmits<{
  toggleDone: [id: string, task: Task];
  removeTask: [id: string];
  clearDone: [];
  clearAll: [];
}>();

const editingId = ref('');
const editingIdRef = ref<HTMLInputElement | null>(null);

watch(editingId, async (id) => {
  if (id) {
    await nextTick();
    editingIdRef.value?.focus();
    editingIdRef.value?.select();
  }
});
</script>

<template>
  <h3 v-if="!tasks.length">Add a task to get started!</h3>

  <div class="tasksheader" v-else>
    <h3>{{ tasksDone.length }} / {{ tasks.length }} tasks completed!</h3>
    <button @click.prevent="emit('clearAll')">Clear All</button>
  </div>

  <TransitionGroup name="list" tag="div" class="classlist">
    <article v-for="task in tasks" :key="task.id" class="task">
      <input
        type="checkbox"
        :checked="task.done"
        @click="emit('toggleDone', task.id, task)"
      />
      <span
        :class="{ done: task.done }"
        class="tasktitle"
        v-if="editingId !== task.id"
        @dblclick="editingId = task.id"
      >
        {{ task.title }}
      </span>
      <input
        v-else
        v-model="task.title"
        @keyup.enter="editingId = ''"
        @blur="editingId = ''"
        @keyup.esc="editingId = ''"
        class="editinput"
        ref="editingIdRef"
      />
      <button class="removebut outline" @click.prevent="emit('removeTask', task.id)">
        Remove
      </button>
    </article>
  </TransitionGroup>

  <div class="donelist">
    <div class="doneheader" v-if="donelist.length">
      <h2>Done:</h2>
      <button @click.prevent="emit('clearDone')">Clear</button>
    </div>
    <article v-for="task in donelist" :key="task.id">
      <span>{{ task.title }}</span>
    </article>
  </div>
</template>

<style>
button {
  height: fit-content;
}

.classlist {
  display: flex;
  flex-direction: column;
  gap: 10px;
  justify-content: space-between;
  margin-top: 20px;
}

.task {
  font-size: 1rem;
  display: flex;
  align-items: center;
}
.tasktitle {
  justify-self: start;
  flex-grow: 1;
  align-self: center;
}

.done {
  text-decoration: line-through;
  color: gray;
}
.doneheader {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 20px;
  transition: all 0.5s ease;
}
.tasksheader {
  display: flex;
  align-items: center;
  justify-content: space-between;
  transition: all 0.5s ease;
}

.removebut {
  height: fit-content;
  scale: 0.8;
}

.list-enter-active,
.list-leave-active {
  transition: all 0.5s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(300px);
}

.editinput {
  width: 100%;
  border-radius: 5px;
  transition: all 0.3s ease;
  transform: translateY(20%);
  height: min-content;
}
</style>
