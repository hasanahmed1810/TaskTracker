<template>
  <div class="container">
    <Header :showAddTask='showAddTask' @toggle-add-task='toggleAddTasks' title="Task Tracker"/>
    <div v-show="showAddTask">
      <AddTask @add-task='addTask'/>
    </div>
    <Tasks @toggle-reminder="toggleReminder" @delete-task='deleteTask' :tasks="tasks" />
  </div>
</template>

<script>

import Header from './components/Header'
import Tasks from './components/Tasks'
import AddTask from './components/AddTask'

export default {
  name: 'App',
  components: {
    Header,
    Tasks,
    AddTask,
  },
  data(){
    return {
      tasks: [],
      showAddTask: false,
    }
  },
  methods: {
    toggleAddTasks(){
      this.showAddTask = !this.showAddTask
    },
    async addTask(task){
      const data = await fetch('http://localhost:5000/tasks', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify(task)
      }).then(res => res.json)
      
      this.tasks = [...this.tasks, data]
    },
    async deleteTask(id){
      if(confirm('Are you sure you want to delete this task?')){
        const res = await fetch(`http://localhost:5000/tasks/${id}`,{
          method: 'DELETE'
        })
        if (res.status === 200){
          this.tasks = this.tasks.filter(task => task.id != id)
        }
      }
    },
    async toggleReminder(id){
      const taskToToggle = await fetch(`http://localhost:5000/tasks/${id}`).then(res => res.json())
      const updatedTask = {
        ...taskToToggle,
        reminder: !taskToToggle.reminder
      }

      await fetch(`http://localhost:5000/tasks/${id}`,{
          method: 'PUT',
          headers: {
            'Content-type': 'application/json'
          },
          body: JSON.stringify(updatedTask)
      }).then(res => res.json)

      this.tasks = this.tasks.map(task => task.id === id ? {...task, reminder: !task.reminder} : task)
    },
  },
  async created(){
    this.tasks = await fetch('http://localhost:5000/tasks').then(res => res.json())
  },
  async updated() {
    this.tasks = await fetch('http://localhost:5000/tasks').then(res => res.json())
  },
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300;400&display=swap');
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}
body {
  font-family: 'Poppins', sans-serif;
}
.container {
  max-width: 500px;
  margin: 30px auto;
  overflow: auto;
  min-height: 300px;
  border: 1px solid steelblue;
  padding: 30px;
  border-radius: 5px;
}
.btn {
  display: inline-block;
  background: #000;
  color: #fff;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  text-decoration: none;
  font-size: 15px;
  font-family: inherit;
}
.btn:focus {
  outline: none;
}
.btn:active {
  transform: scale(0.98);
}
.btn-block {
  display: block;
  width: 100%;
}
</style>
