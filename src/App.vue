<template>
  <div id="app">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App',
  created(){
    var theme = window.localStorage.getItem('theme');
    if(theme == 'dark'){
      this.$changeTheme('dark');
    }
  }
}
</script>

<style>
#app {
width: 100%;
height: 100%;
}
</style>
