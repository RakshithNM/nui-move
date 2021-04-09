<template>
  <Ball msg="Speech Recognition Circle Animation" />
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import Ball from './components/Ball.vue'

export default defineComponent({
  name: 'App',
  components: {
    Ball 
  }
})
</script>

<style>
body {
 margin: 0;
 background: #011627;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: limegreen;
  margin-top: 60px;
}
</style>
