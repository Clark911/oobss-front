<style scoped>

</style>

<template>
  <el-collapse accordion value="todo" :style="{height:'100%'}">
    <el-collapse-item title="拖后" name="later">
      <el-table :data="lastTasks" style="width: 100%" :showHeader=false>
        <el-table-column
          label="任务"
          prop="name">
        </el-table-column>
        <el-table-column fixed="right" width="180">
          <template slot-scope="item">
            <el-button v-on:click="backTask(item.row.id)" size="small">Redo</el-button>
            <el-button v-on:click="deleteTask(item.row.id)" size="small">Delete</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-collapse-item>
    <el-collapse-item title="今日待办" name="todo">
      <el-table :data="currentTasks" style="width: 100%" :showHeader=false>
        <el-table-column
          label="任务"
          prop="name">
        </el-table-column>
        <el-table-column fixed="right" width="180">
          <template slot-scope="item">
            <el-button size="small" v-bind:loading="btnLodingStatus[item.row.id]"  v-on:click="fixTask(item.row.id)">Done</el-button>
            <el-button size="small" v-bind:loading="btnLodingStatus[item.row.id]" v-on:click="deleteTask(item.row.id)">Delete</el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-collapse-item>
    <el-collapse-item title="完成了" name="done">
      <ul>
      <li v-for="item in finishedTasks" v-bind:key="item.id">
        <div class="task" v-on:mouseenter="showBackBtn">
          <div class="task-text">
            {{ item.name }}
          </div>
          <div class="btn-finish">
            <el-button v-bind:loading="btnLodingStatus[item.id]" v-on:click="backTask(item.id)">Redo</el-button>
            <el-button v-bind:loading="btnLodingStatus[item.id]" v-on:click="deleteTask(item.id)"></el-button>
          </div>
        </div>
      </li>
      </ul>
    </el-collapse-item>
  </el-collapse>
</template>
<script>
export default {
  name: 'Home',
  data () {
    return {
      currentTasks: [],
      btnLodingStatus: {},
      lastTasks: [],
      finishedTasks: [],
      taskName: ''
    }
  },
  mounted() {
    this.homeTasks()
  },
  methods: {
    homeTasks: function(){
      // this.$Loading.start();
      var $this = this
      this.$http.get("http://api.oobss.com/tasks/main").then(function(response) {
        $this.btnLodingStatus = {}
        $this.currentTasks = response.data.data.currentTasks
        $this.lastTasks = response.data.data.lastTasks
        $this.finishedTasks = response.data.data.finishedTasks
        // $this.$Loading.finish();
      }).catch(function(error) {
        console.log(error)
        // $this.$Loading.error();
      })
    },
    putTask: function() {
      var $this = this
      this.$http.post("http://api.oobss.com/tasks",{name:$this.taskName}).then(function(response){
        if(response.data.retCode != 1){
            $this.$Message.error(response.data.errMsg);
        }
        $this.homeTasks()
        $this.taskName = ''
      }).catch(function(error) {
        console.log(error)
      })
    },
    showDoneBtn: function(e) {
        // debugger
    },
    showBackBtn: function(e){

    },
    fixTask: function(taskId){
      var $this = this
      this.$set(this.btnLodingStatus, taskId, true)
      this.$http.patch("http://api.oobss.com/tasks",{id:taskId}).then(function(response){
        if(response.data.retCode != 1){
            $this.$Message.error(response.data.errMsg);
        }
        $this.homeTasks()
      }).catch(function(error) {
        console.log(error)
      })
    },
    backTask: function(taskId){
      var $this = this
      this.$set(this.btnLodingStatus, taskId, true)
      this.$http.patch("http://api.oobss.com/tasks/back",{id:taskId}).then(function(response){
        if(response.data.retCode != 1){
            $this.$Message.error(response.data.errMsg);
        }
        $this.homeTasks()
      }).catch(function(error) {
        console.log(error)
      })
    },
    deleteTask: function(taskId){
      var $this = this
      this.$http.delete("http://api.oobss.com/tasks",{params:{id:taskId}}).then(function(response){
        if(response.data.retCode != 1){
            $this.$Message.error(response.data.errMsg);
        }
        $this.homeTasks()
      }).catch(function(error) {
        console.log(error)
      })
    },
    handleReachBottom (){
      // alert('ok')
    }
  }
}
</script>