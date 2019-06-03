<template lang="pug">
  #app
    //- pre show modale = {{ showModale }}
    button(v-if="!showModale" @click="showModale = true") Show Modale
    button(@click="openModalProgrammatic") Open a modale

    modal(:active.sync="showModale" :canCancel="opts")
      modal-fixed-wrapper
        div(slot="top") Header
        div(style="height: 6000px") Content
        div(slot="bottom") Footer
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'
import ModalFixedWrapper from './components/ModalFixedWrapper.vue'

@Component({
  components: {
    ModalFixedWrapper
  },
})
export default class App extends Vue {
  showModale = true
  $modal: any

  get opts() {
    return ['escape', 'outside', 'button']
  }

  openModalProgrammatic() {
    this.$modal.open({
      parent: this,
      component: ModalFixedWrapper
    })
  }
}
</script>
