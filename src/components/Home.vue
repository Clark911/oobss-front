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
  /* border: 1px solid black; */
}
.btn-finish{
    float: right;
    padding-top: 4px;
    /* border: 1px solid black; */
}
</style>

<template>
  <div>
    <Card>
      <div style="height: 100px">
        <h1>拖后</h1>
        <ul>
        <li v-for="item in lastTasks" v-bind:key="item.id">{{ item.name }}({{item.time}})</li>
        </ul>
      </div>
    </Card>

    <Card>
      <div style="height: 400px">
        <h1>今日待办</h1>
        <Scroll :on-reach-bottom="handleReachBottom">
            <ul>
            <li v-for="item in currentTasks" v-bind:key="item.id">
                <div class="task" v-on:mouseenter="showDoneBtn">
                    <div class="task-text">
                        {{ item.name }}
                    </div>
                    <div class="btn-finish">
                      <Button type="text" shape="circle" icon="checkmark-round" v-bind:loading="loading"  v-on:click="fixTask(item.id)"></Button>
                    </div>
                </div>
            </li>
            </ul>
        </Scroll>
        <input placeholder="添加任务······" v-on:keyup.enter="putTask" v-model="taskName">
      </div>
    </Card>

    <Card>
      <div style="height: 100px">
        <h1>完成了</h1>
        <ul>
        <li v-for="item in finishedTasks" v-bind:key="item.id">{{ item.name }}</li>
        </ul>
      </div>
    </Card>
  </div>
</template>
<script>
export default {
  name: 'Home',
  data () {
    return {
      currentTasks: [],
      lastTasks: [],
      finishedTasks: [],
      taskName: '',
      loading: false
    }
  },
  mounted() {
    this.homeTasks()
  },
  methods: {
    homeTasks: function(){
      this.$Loading.start();
      var $this = this
      this.$http.get("http://api.oobss.com/tasks/main").then(function(response) {
        $this.currentTasks = response.data.data.currentTasks
        $this.lastTasks = response.data.data.lastTasks
        $this.finishedTasks = response.data.data.finishedTasks
        $this.$Loading.finish();
      }).catch(function(error) {
        console.log(error)
        $this.$Loading.error();
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
    fixTask: function(taskId){
      this.loading = true;
      var $this = this
      this.$http.patch("http://api.oobss.com/tasks",{id:taskId}).then(function(response){
        if(response.data.retCode != 1){
            $this.$Message.error(response.data.errMsg);
        }
        $this.homeTasks()
        $this.loading = false;
      }).catch(function(error) {
        console.log(error)
      })
    },
    handleReachBottom (){

    }
  }
}
</script>