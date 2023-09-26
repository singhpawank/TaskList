<script setup>
import { ref } from "vue";

const title = ref("");
const details = ref("");
const progressMarks = ref([]);
const progressMark = ref("");
let selectedOption =  "";
const activeFilter = ref("all");

const tasks = ref([]);
const errorMessage = ref("");
const errorMessage2 = ref("");
function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 75%, 75%";
}

const createTask = () => {
  if (title.value.length === 0) {
    errorMessage.value = "Title can not be empty!";
    return;
  }
  tasks.value.push({
    id: Math.floor(Math.random() * 1000000),
    title: title.value,
    details: details.value,
    status: "start",
    date: new Date(),
    backgroundColor: getRandomColor(),
  });
  
  title.value = "";
  details.value = "";
  errorMessage.value = "";

  if(activeFilter==="all"){
    allTasks();
  }else if(activeFilter==="incomplete"){
    incompleteTasks();
  }
};

function deleteTask(taskId) {
  const taskIndex = tasks.value.findIndex((task) => task.id === taskId);
  if (taskIndex !== -1) {
    tasks.value.splice(taskIndex, 1);
  }
}

function makeProgress(taskId){
  const taskIndex = tasks.value.findIndex((task) => task.id === taskId);
  if (taskIndex !== -1) {
    tasks.value[taskIndex].status = selectedOption;
  }
  
  if(activeFilter===selectedOption){
    filterTask(selectedOption);
  }
  selectedOption="";
}

const showTasks = ref(tasks.value);

function filterTask(mark){
  activeFilter.value = mark;
  if(mark==="all"){
    showTasks.value = tasks.value;
    return;
  }
  showTasks.value = tasks.value.filter((task) => task.status===mark);
}

function addProgressMark(){
  if(progressMark.value.length===0){
    errorMessage2.value = "Progress mark can not be empty!";
    return;
  }
  progressMarks.value.push(progressMark.value);
  progressMark.value="";
  errorMessage2.value="";
}
</script>

<template>
  <div class="container">
    <header>
      <h1>Task List</h1>
    </header>

    <div class="create">
      <input class="marks" v-model.trim="progressMark" type="text" placeholder="progress marks"/>
      <p v-if="errorMessage2">{{ errorMessage2 }}</p>
      <button @click="addProgressMark">Add Progress Mark</button>
    </div>

    <div class="create">
      <input v-model.trim="title" type="text" placeholder="Title..."/>
      <textarea v-model.trim="details" name="task" id="task" cols="30" rows="4" placeholder="Details..."></textarea>
      <p v-if="errorMessage">{{ errorMessage }}</p>
      
      <button @click="createTask" >Create Task</button>
    </div>
    <div class="filter">
      <button @click="filterTask('all')" :class="{ 'active-filter': activeFilter === 'all' }">All</button>
      <button @click="filterTask(mark)" v-for="mark in progressMarks" :class="{ 'active-filter': activeFilter === mark }">{{ mark }}</button>
    </div>
    
    <div class="tasks">
       <div v-for="task in showTasks" :key="task.id" class="card" :style="{backgroundColor:task.backgroundColor}">
        <div class="title-bar">
        <h2>{{ task.title }}</h2>
        <button class="delete" @click="deleteTask(task.id)"><img src="/images/delete.png" alt="delete"/></button>
        </div>  
          <p class="details">{{ task.details }}</p>
          
          <div class="status">
            <p>{{ task.status}}</p>
            <label for="dropdown">Select an option:</label>
            <select id="dropdown" v-model="selectedOption" @change="makeProgress(task.id)">
              <option v-for="mark in progressMarks" :key="mark" :value="mark">{{ mark }}</option>
            </select>
            {{ selectedOption }}
        </div>
        </div>
    </div>
  </div>
</template>

<style> 
.container {
   max-width: 1000px;
   margin: 0 auto;
   background-color: white;
   min-height: 100vh;
 }

 header h1 {
   background-color: rgb(182, 127, 25);
   text-align: center;
   color: black;
   font-weight: 900;
 }

 .filter {
   display: flex;
   flex-wrap: wrap;
   /* justify-content: space-between; */
   align-items: center;
   margin: auto;
   /* margin: 10px 20px; */
 }

 .filter button {
   border: none;
   border-radius: 5px;
   margin-right: 10px;
   width: 100px;
   height: 40px;
   background-color: rgb(182, 127, 25);
   font-size: 15px;
   font-weight: 700;
   cursor: pointer;
   margin: 10px;
 }

 .active-filter {
  background-color: rgb(144, 96, 7) !important; 
  color: white;
}

 .create {
   margin: 10px auto;
   width: 400px;
   background-color: rgb(228, 220, 220);
   border-radius: 10px;
   padding: 10px;
   position: relative;
   display: flex;
   flex-direction: column;
 }
 
 .create input{
  padding: 4px 5px;
  font-size: 15px;
  border: none;
  border-radius: 3px;
  margin-bottom: 8px;
}

.create textarea{
  padding: 4px 5px;
  font-size: 14px;
  border: none;
  border-radius: 3px;
}
 .create button{
   padding: 8px 20px;
   font-size: 15px;
   width: 100%;
   background-color: rgb(182, 127, 25);
   border: none;
   border-radius: 5px;
   color: white;
   cursor: pointer;
   /* margin-top: 8px; */
}
.create p{
  color: rgb(193, 15, 15);
}

.tasks{
  display: grid;
  grid-template-columns: 33% 33% 33%;
  justify-content: space-between;
  margin: 20px auto;
  max-width: 960px;
  padding: 0 10px;
}

@media (max-width: 900px) {
  .tasks{
    grid-template-columns: 50% 50%;
  }
}

@media (max-width:600px) {
  .tasks{
    grid-template-columns: 100%;
  }
}
.card{
  width: 280px;
  padding: 5px 10px;
  background-color: rgb(123, 32, 45);
  display: flex;
  flex-direction: column;
  margin:10px auto;
  border-radius: 8px;
  color: black;
}

.title-bar{
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card h2{
  font-size: 18px;
  font-weight: 500;
}

.delete{
  border: none;
  background: none;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
}
.delete img{
 height: 18px;
 background: none;
}
.card .details{
  min-height: 20px;
}

.status{
  display: flex;
  justify-content: space-between;
  margin-top: auto;
  height: 24px;
}
.status p{
  font-size: 14px;
  font-weight: bold;
  color: green;
}

.status div{
  display: flex;
  align-items: center;
}

.status > div > label{
   margin-right: 5px;
}

option{
  width: 100px;
  height: 20px;
}

.marks{
  margin-top: 8px;
}
</style>
