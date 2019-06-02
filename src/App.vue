<template>
  <div id="app">
    <h1>Tasks</h1>
    <TasksProgress :progress="progress"/>
    <NewTask @taskAdded="addTask"/>
    <TaskGrid :tasks="tasks" @taskDeleted="deleteTask" @taskStateChanged="toggleTaskState"/>
  </div>
</template>

<script>
import TasksProgress from "./components/TaskProgress.vue";
import NewTask from "./components/NewTask.vue";
import TaskGrid from "./components/TaskGrid.vue";

export default {
  components: { TasksProgress, NewTask, TaskGrid },
  data() {
    return {
      tasks: []
    };
  },
  computed: {
    progress() {
      const total = this.tasks.length;
      const done = this.tasks.filter(t => !t.pending).length;
      return Math.round((done / total) * 100) || 0;
    }
  },

	watch:{
		tasks:{
			deep: true,
			handler(){
				localStorage.setItem('tasks', JSON.stringify(this.tasks))
			}
			
		}
	},

  methods: {
    addTask(task) {
      const sameName = t => t.name === task.name;
      const reallyNew = this.tasks.filter(sameName).length == 0;
      if (reallyNew) {
        this.tasks.push({
          name: task.name,
          pending: task.pending || true
        });
      }
    },

    deleteTask(i) {
      this.tasks.splice(i, 1);
    },

    toggleTaskState(i) {
      this.tasks[i].pending = !this.tasks[i].pending;
    }
  },

  created(){
	  const json = localStorage.getItem('tasks')
	  const array = JSON.parse(json) 
	  this.tasks = Array.isArray(array) ? array : []
		
  }
};
</script>

<style>
body {
  font-family: "Lato", sans-serif;
  color: #fff;
  background: #44a08d; /* fallback for old browsers */
  background: -webkit-linear-gradient(
    to right,
    #093637,
    #44a08d
  ); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(
    to right,
    #093637,
    #44a08d
  ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
}

#app {
  display: flex;
  flex: 1;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

#app h1 {
  margin-bottom: 5px;
  font-weight: 300;
  font-size: 3rem;
}
</style>
