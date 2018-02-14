<style>
    .layout{
        border: 1px solid #d7dde4;
        background: #f5f7f9;
        position: relative;
        border-radius: 4px;
        overflow: hidden;
    }
</style>
<template>
    <div class="layout" id="app">
        <!-- <Sider :style="{position: 'fixed', height: '100vh', left: 0, overflow: 'auto'}">

        </Sider> -->
        <Layout :style="{marginLeft: '0'}">
            <Content :style="{padding: '0 16px 16px'}">
                <router-view/>
            </Content>
        </Layout>
    </div>
</template>
<script>
export default {
  name: 'app'
}
</script>