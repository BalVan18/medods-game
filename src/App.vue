<template>
  <div id="app">
    <SimonTheGame />
  </div>
</template>

<script>
import SimonTheGame from './components/SimonTheGame.vue'

export default {
  name: 'App',
  components: {
    SimonTheGame
  }
}
</script>

<style lang="sass">
  @import '@/assets/style/main'
</style>
