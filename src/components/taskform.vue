<script lang="ts" setup>
import { ref } from 'vue';
const newTask = ref('');    
const pholder = ref('Add a new task...');
const errorvalue = ref('');;

const emit = defineEmits<{
    addTask: [newTask: string]
}>();

function addTask(){
    if (newTask.value.trim()){
        emit('addTask', newTask.value);
        newTask.value ='';
    } else {
        errorvalue.value = 'You cannot submit an empty task!!'
    }



}



</script>
<template>
     <main>
    <form @submit.prevent="addTask">
      <div class="biggercontainer">
      <div class="bigcontainer">  
      <input 
      v-model="newTask" 
      name="newTask" 
      v-bind:placeholder="pholder"
      :aria-invalid="!!errorvalue || undefined"  
      @input="errorvalue = ''"
      />
     
      <div class="button-container">
        <button>Add</button>
      </div>
        </div>
        <small v-if="errorvalue" id="invalid-helper">
       {{ errorvalue}}
        </small>    
        </div>
    </form>
   
  </main>


</template>
<style>

main {
  margin-bottom: 150px;
  margin-top: 30px;
  position: sticky;
  top: 5%;
  background: transparent;
  backdrop-filter: blur(15px);
  height: fit-content;
}
</style>