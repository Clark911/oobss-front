<style scoped>
.task{
  min-width: 200px;
  width: 100;
  min-height: 26px;
  padding: 2px;
  overflow: hidden;
}
.task:hover{
  background-color: beige;
}
.task-text{
  float: left;
  max-width: 800px;
  min-width: 180px;
  word-break: break-all;
  -webkit-line-clamp: 1;
  overflow: hidden;
  display: -webkit-box;
  -webkit-box-orient: vertical;
  white-space: normal;
}
.btn-finish{
  float: right;
  /* padding-top: 4px; */
  /* border: 1px solid black; */
}
</style>

<template>
<el-collapse accordion value="todo" :style="{height:'100%'}">
  <el-collapse-item name="later">
    <template slot="title">
      拖后<i class="header-icon el-icon-info"></i>
    </template>
    <ul>
      <li v-for="item in lastTasks" v-bind:key="item.id">
        <div class="task" v-on:mouseenter="showBackBtn">
          <div class="task-text">
            {{ item.name }}({{item.time}})
          </div>
          <div class="btn-finish">
            <el-button v-on:click="backTask(item.id)">Redo</el-button>
            <el-button v-on:click="deleteTask(item.id)"></el-button>
          </div>
        </div>
      </li>
    </ul>
  </el-collapse-item>
  <el-collapse-item title="今日待办" name="todo">
    <ul>
      <li v-for="item in currentTasks" v-bind:key="item.id">
          <div class="task" v-on:mouseenter="showDoneBtn">
              <div class="task-text">
                {{ item.name }}
              </div>
              <div class="btn-finish">
                <el-button v-bind:loading="btnLodingStatus[item.id]"  v-on:click="fixTask(item.id)">Done</el-button>
                <el-button v-bind:loading="btnLodingStatus[item.id]" v-on:click="deleteTask(item.id)"></el-button>
              </div>
          </div>
      </li>
      </ul>
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
  <!-- <div>
    <Card>
      <div style="height: 200px">
        <h1>拖后</h1>
        <Scroll :on-reach-bottom="handleReachBottom" height="150">
          <ul>
            <li v-for="item in lastTasks" v-bind:key="item.id">
              <div class="task" v-on:mouseenter="showBackBtn">
                <div class="task-text">
                  {{ item.name }}({{item.time}})
                </div>
                <div class="btn-finish">
                  <Button shape="circle" icon="reply" size="small" v-on:click="backTask(item.id)">Redo</Button>
                  <Button type="dashed" shape="circle" icon="trash-a" size="small" v-on:click="deleteTask(item.id)"></Button>
                </div>
              </div>
            </li>
          </ul>
        </Scroll>
      </div>
    </Card>

    <Card>
      <div style="height: 400px">
        <h1>今日待办</h1>
        <Scroll :on-reach-bottom="handleReachBottom">
            <ul>
            <li v-for="item in currentTasks" v-bind:key="item.id">
                <div class="task" v-on:mouseenter="showDoneBtn">
                    <Tooltip max-width="200" placement="top" v-bind:content="item.name">
                        <div class="task-text">
                          {{ item.name }}
                        </div>
                    </Tooltip>
                    <div class="btn-finish">
                      <Button shape="circle" v-bind:loading="btnLodingStatus[item.id]" icon="checkmark-round" size="small" v-on:click="fixTask(item.id)">Done</Button>
                      <Button type="dashed" shape="circle" icon="trash-a" size="small" v-bind:loading="btnLodingStatus[item.id]" v-on:click="deleteTask(item.id)"></Button>
                    </div>
                </div>
            </li>
            </ul>
        </Scroll>
        <Input placeholder="添加任务······" v-on:on-enter="putTask" v-model="taskName"></Input>
      </div>
    </Card>

    <Card>
      <div style="height: 200px">
        <h1>完成了</h1>
        <Scroll :on-reach-bottom="handleReachBottom" height="150">
          <ul>
          <li v-for="item in finishedTasks" v-bind:key="item.id">
            <div class="task" v-on:mouseenter="showBackBtn">
              <div class="task-text">
                  {{ item.name }}
              </div>
              <div class="btn-finish">
                <Button shape="circle" icon="reply" size="small" v-bind:loading="btnLodingStatus[item.id]" v-on:click="backTask(item.id)">Redo</Button>
                <Button type="dashed" shape="circle" icon="trash-a" size="small" v-bind:loading="btnLodingStatus[item.id]" v-on:click="deleteTask(item.id)"></Button>
              </div>
            </div>
          </li>
          </ul>
        </Scroll>
      </div>
    </Card>
  </div> -->
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