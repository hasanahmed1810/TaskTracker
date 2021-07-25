<template>
    <div v-show="showAddTask">
      <AddTask @add-task='addTask'/>
    </div>
    <Tasks @toggle-reminder="toggleReminder" @delete-task='deleteTask' :tasks="tasks" />
</template>

<script>
import Tasks from '../components/Tasks'
import AddTask from '../components/AddTask'

export default {
    name: 'Home',
    props: {
        showAddTask: Boolean,
    },
    components: {
        Tasks,
        AddTask,
    },
    data(){
        return{
            tasks: [],
        }
    },
    methods: {
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
      })

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
    
</style>