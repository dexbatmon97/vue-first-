<template>
  <AddTask v-show="showAddTask" @add-task="addTask" />
  <Tasks
    @toggle-reminder="toggleReminder"
    @delete-task="deleteTask"
    :tasks="tasks"
  />
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
  data() {
    return {
      tasks: [],
    }
  },
  methods: {
    async addTask(task) {
      const res = await fetch('http://localhost:5000/tasks', {
        method: 'POST',
        headers: {
          'Content-type': 'application/json',
        },
        body: JSON.stringify(task),
      })
      const data = await res.json()
      this.tasks = [...this.tasks, data]
    },
     async  deleteTask(id){  
       if(confirm("are u sure?")){
       
         const res = await fetch(`http://localhost:5000/tasks/${id}`,
        {
          method: 'DELETE',
        })
         console.log(this.tasks),

        res.status == 200 ? (this.tasks = this.tasks.filter((task) => task.id !== id )) : alert("error")
        
        
        console.log(this.tasks)
       }
     },
      async toggleReminder(id){
       // console.log(id, this.tasks[id-1].reminder)
        const taskToToggle = await this.fetchTask(id)
        console.log(taskToToggle);
        const updTask={...taskToToggle, reminder : !taskToToggle.reminder}
        
        const res = await fetch(`http://localhost:5000/tasks/${id}`,
        {
          method: 'PUT',
          headers: {
            'Content-type': 'application/json',
          },
          body: JSON.stringify(updTask),
        })
       const data = await res.json();
       console.log(data)
       this.tasks[id-1].reminder= !this.tasks[id-1].reminder
      // this.task = this.task.map( (task) => task.id == id ? {...task, reminder: data.reminder } : task)
      // console.log(id, this.tasks[id-1].reminder)
     },
     async fetchTasks(){
        const res = await fetch('http://localhost:5000/tasks')

        const data = await res.json()

        return data
     },
       async fetchTask(id){
        const res = await fetch(`http://localhost:5000/tasks/${id}`)

        const data = await res.json()

        return data
     },
  },
  async created() {
    this.tasks = await this.fetchTasks()
  },
}
</script>