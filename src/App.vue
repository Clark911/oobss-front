<style>
    .layout{
        border: 1px solid #d7dde4;
        background: #f5f7f9;
        position: relative;
        border-radius: 4px;
        overflow: hidden;
        height: 100%;
    }
</style>
<template>
    <div class="layout" id="app">
        <el-container :style="{height:'100%'}">
            <el-main :style="{height:'100%'}">
                <router-view/>
            </el-main>
        </el-container>
    </div>
</template>
<script>
export default {
  name: 'app'
}
</script>