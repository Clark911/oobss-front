<template>
  <div class="hello">
    <h1>拖后</h1>
    <ul>
      <li v-for="item in lastTasks" v-bind:key="item.id">{{ item.name }}({{item.time}})</li>
    </ul>
    <h1>今日待办</h1>
    <ul>
      <li v-for="item in currentTasks" v-bind:key="item.id">{{ item.name }}</li>
    </ul>
    <input placeholder="添加任务······" v-on:keyup.enter="putTask" v-model="taskName">
    <h1>完成了</h1>
    <ul>
      <li v-for="item in finishedTasks" v-bind:key="item.id">{{ item.name }}</li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      currentTasks: [],
      lastTasks: [],
      finishedTasks: [],
      taskName: ''
    } 
  },
  mounted(){
    this.homeTasks()
  },
  methods: {
    homeTasks: function(){
      var $this = this
      this.$http.get("http://localhost:8080/tasks/main").then(function(response){
        $this.currentTasks = response.data.data.currentTasks
        $this.lastTasks = response.data.data.lastTasks
        $this.finishedTasks = response.data.data.finishedTasks
      }).catch(function(error){
        console.log(error)
      })
    },
    putTask: function(){
      var $this = this
      this.$http.post("http://localhost:8080/tasks",{name:$this.taskName}).then(function(response){
        $this.homeTasks()
        $this.taskName = ''
      }).catch(function(error){
        console.log(error)
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  /* display: inline-block; */
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
