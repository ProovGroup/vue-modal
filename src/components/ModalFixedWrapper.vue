<template lang="pug">
  .modal-fixed-wrapper
    .modal-fixed-top
      slot(name="top")
      div(@click="closeModale") close
    .modal-fixed-content
      slot
    .modal-fixed-bottom(v-if="!!$slots.bottom")
      slot(name="bottom")
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator'

@Component
export default class ModalFixedWrapper extends Vue {
  $parent: any

  closeModale() {
    this.$parent.close()
  }

}
</script>

<style scoped lang="sass">
  .modal-fixed-wrapper
    .modal-fixed-top,
    .modal-fixed-bottom
      position: sticky

    .modal-fixed-top
      top: 0
      display: flex
      justify-content: flex-end

    .modal-fixed-bottom
      bottom: 0
</style>
