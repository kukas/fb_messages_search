<template>
  <div id="app">
    <Messages/>
  </div>
</template>

<script>
import Messages from './components/Messages'

export default {
  name: 'App',
  components: {
    Messages
  }
}
</script>

<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
  color: #2c3e50;
  width: 800px;
  margin: 0px auto;
}
@media screen and (max-width: 800px) {
  #app {
    width: auto;
  }
}

</style>
