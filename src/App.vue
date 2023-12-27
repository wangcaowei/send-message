<template>
  <div id="app">
    <MessageWindow />
  </div>
</template>

<script>
import MessageWindow from './components/MessageWindow.vue'

export default {
  name: 'App',
  components: {
    MessageWindow
  }
}
</script>

<style>
#screenShotContainer {
  left: 0 !important;
  top: 0 !important;
}
</style>
