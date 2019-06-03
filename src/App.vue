<template lang="pug">
  #app
    modal
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import Modal from './components/Modal.vue'

@Component({
  components: {
    Modal,
  },
})
export default class App extends Vue {}
</script>

<style lang="scss">
#app {
}
</style>
