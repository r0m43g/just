<template lang="pug">
slot
  img(alt='Vue logo', src='./assets/fire128.png')
  Popup
</template>

<script>
import Popup from './components/Popup.vue'

export default {
  name: 'App',
  components: {
    Popup
  }
}
</script>

<style lang="sass">
#app
  font-family: Avenir, Helvetica, Arial, sans-serif
  -webkit-font-smoothing: antialiased
  -moz-osx-font-smoothing: grayscale
  text-align: center
  color: #2c3e50
  margin-top: 20px
  min-width: 640px
  min-height: 640px

</style>
