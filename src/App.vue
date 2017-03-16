<template>
  <div id="app">
  <div style="width:20px;height:100px"></div>
    <Affix>
        <span class="demo-affix">固定在距离顶部 50px 的位置</span>
    </Affix>
    <div style="width:20px;height:1000px"></div>
  </div>
</template>

<script>

import Affix from './components/affix'

export default {
  name: 'app',
  components:{'Affix':Affix}
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  /*margin-top: 60px;*/
}
.demo-affix{
    display: inline-block;
    color: #fff;
    padding: 10px 30px;
    text-align: center;
    background: rgba(0,153,229,.9);
}
</style>
