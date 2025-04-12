
<script setup lang="ts">
import { ref, onMounted, watch} from 'vue';
import TaskForm from './components/taskform.vue';
import type { Task } from './components/types'; 
import Tasklist from './components/tasklist.vue';
//@ts-ignore
import JSConfetti from 'js-confetti';

const confetti = new JSConfetti()

function showConfetti() {
  confetti.addConfetti()
}



onMounted(()=> {
  const savedTasks = localStorage.getItem('tasks');
  const savedtasksDone = localStorage.getItem('tasksdone');
  if (savedtasksDone) tasksdone.value = JSON.parse(savedtasksDone);
  if (savedTasks) tasks.value = JSON.parse(savedTasks);
  donelist.value = tasks.value.filter(task => task.done);
})
const tasks = ref<Task[]>([]);
function addTask(newTask: string){

  if (newTask){
    tasks.value.push({
      id: crypto.randomUUID(),
      title: newTask,
      done: false,
    })
    newTask = '';
  }
}
const donelist = ref<Task[]>([]);

const tasksdone = ref<Task[]>([]);
function toggleDone ( id: string, task: Task){
  const taskI = tasks.value.find((task) => task.id === id);
  const taskDoneI = tasksdone.value.find((task)=> task.id === id)
  if (taskI){
    taskI.done = !taskI.done;
    if (taskI.done){
    
      if( !taskDoneI){donelist.value.push(taskI);}
      tasksdone.value.push(taskI);
    } else {
      tasksdone.value = tasksdone.value.filter((task) => task.id !== id);
      donelist.value = donelist.value.filter((task)=> task.id !== id);
    }
  }
  if (tasksdone.value.length > 0 && tasks.value.length === tasksdone.value.length){
    showConfetti();
    setTimeout(()=> {
      alert('All tasks done! Well done!');
    }, 1500);
    }
  }
function Removetask (id:string){
  const taskI = tasks.value.find((task)=> task.id === id);
  if (taskI){
    tasks.value = tasks.value.filter((task) => task.id !== id);
    
  }
  const taskDoneI = tasksdone.value.find((task)=> task.id === id);
  if (taskDoneI){
    tasksdone.value = tasksdone.value.filter((task)=> task.id !== id);
  }
}
function clearDone(){
  donelist.value.forEach((task)=> {
    if (task.done) task.done = false;
  });
  donelist.value = [];
  tasksdone.value = [];
}


watch(tasks, (newTasks)=> {
  localStorage.setItem('tasks', JSON.stringify(newTasks));
}, {deep: true});

watch(tasksdone, (newDone)=> {
  localStorage.setItem('tasksdone', JSON.stringify(newDone));

}, {deep:true });



</script>
<template>
 <main> 
  
  <TaskForm @add-task="addTask"/>

<Tasklist 
  @toggle-done="toggleDone" 
  :tasks="tasks"  
  :tasks-done="tasksdone" 
  :donelist="donelist"
  @remove-task="Removetask"
  @clear-done="clearDone"
  @clear-all="tasks = []; donelist =[]; tasksdone = []"
/> 
</main>
</template>

<style>
*{
  box-sizing: border-box;
  margin: 20p;
  padding: 0;
}

.button-container{
display:  flex;
justify-content: end;
height: fit-content;
  
}
.biggercontainer{
  display: flex;
  flex-direction: column;
  justify-content: center;
}
.bigcontainer{
  display: flex;
  flex-direction: row;
  gap: 10px;
  justify-content: center;
  justify-content: space-between;

}
#app{
  width: 100%;
 justify-content: center;
 margin: 0 auto;
  display: flex;
  flex-direction: column;

}
</style> 