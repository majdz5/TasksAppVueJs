<script lang="ts" setup>
import { ref, watch, nextTick } from 'vue';
import type { Task } from './types';
import Vuedraggable from 'vuedraggable';  

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

  <Vuedraggable 
    :list="tasks" 
    class="draggable-list" 
    item-key="id" >
    <template #item="{ element }">
      <article :key="element.id" class="task">
        <input
          type="checkbox"
          :checked="element.done"
          @click="emit('toggleDone', element.id, element)"
        />
        <span
          :class="{ done: element.done }"
          class="tasktitle"
          v-if="editingId !== element.id"
          @dblclick="editingId = element.id"
        >
          {{ element.title }}
        </span>
        <input
          v-else
          v-model="element.title"
          @keyup.enter="editingId = ''"
          @blur="editingId = ''"
          @keyup.esc="editingId = ''"
          class="editinput"
          ref="editingIdRef"
        />
        <button class="removebut outline" @click.prevent="emit('removeTask', element.id)">
          Remove
        </button>
      </article>
    </template>
  </Vuedraggable>

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
  margin-bottom: 1rem;

}

.removebut {
  height: fit-content;
  scale: 0.8;
}



.editinput {
  width: 100%;
  border-radius: 5px;
  transition: all 0.3s ease;
  transform: translateY(20%);
  height: min-content;
}
</style>
