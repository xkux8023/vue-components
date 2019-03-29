<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Index</router-link>|
      <router-link to="/form">具有数据校验功能的表单组件 —— Form</router-link>
      <router-link to="/checkbox">复选框组件 —— Checkbox </router-link>
      <router-link to="/display">动态渲染组件 —— Display </router-link>
    </div>
    <router-view/>
  </div>
</template>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
