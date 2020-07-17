<template>
  <div id="app">
    <h1 id="app-title">
      <img src="./assets/logo.png">
      <span>Todo App</span><br>
    </h1>
    <span><small>by Mouad Benali</small></span>
    <todo-app/>
  </div>
</template>

<script>
import TodoApp from './components/TodoApp'

export default {
  name: 'App',
  components: {
    TodoApp
  }
}
</script>

<style>
*{
  box-sizing: border-box;
}
body {
    margin: 0;
    overflow-x: hidden;
    -moz-osx-font-smoothing: grayscale;
    -webkit-font-smoothing: antialiased;
    font-family: 'Roboto','Avenir', Helvetica, Arial, sans-serif;
    font-size: 16px;
    background-color: #f0f0f0;
    color: #202124;
    line-height: 1.5;
}


#app {
  text-align: center;
  color: #2c3e50;
  padding: 50px 100px;
}
#app-title{
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
}

#app-title img{
  height: 45px;
  width: 45px;
  margin: 10px;
}
small{
  display: block;
  font-size: 11px;
  font-weight: 500;
  margin-top: -45px;
  margin-left: 25px;
  margin-bottom: 40px;
}


@media screen and (max-width: 850px){
  #app {
      padding: 50px;
  }
}
@media screen and (max-width: 480px){
  #app {
      padding: 30px;
  }
}
</style>
