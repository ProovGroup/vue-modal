<template lang="pug">
  transition(:name="animation")
    .modal-wrapper.is-active(v-if="isActive")
      .modal-overlay(@click="cancel('outside')")
      .modal-content
        component(v-if="component" v-bind="props" :is="component" v-on="events" @close="close")
        div(v-else-if="content" v-html="content")
        slot(v-else)
      button.modal-close(@click="close" v-if="showClose") Close
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch } from 'vue-property-decorator'
import { removeElement } from '@juno.dev/js-swiss-knife'

@Component
export default class Modal extends Vue {
  @Prop({ type: Boolean, default: false }) active!: boolean
  @Prop({ type: [Array, Boolean], default: () => ['escape', 'x', 'outside', 'button'] }) canCancel!: []
  @Prop({ type: Boolean, default: false }) programmatic!: boolean
  @Prop({ type: String, default: 'zoom-out' }) animation!: string
  @Prop({ type: Function, default: () => {} }) onCancel!: () => void

  @Prop({ type: String }) content!: string
  @Prop({ type: [Object, Function] }) component!: any
  @Prop({ type: Object }) props!: {}
  @Prop({ type: Object }) events!: {}

  isActive: boolean = this.active || false

  @Watch('active')
  updateActive(value: boolean) {
    this.isActive = value
  }

  get cancelOptions() {
    return typeof this.canCancel === 'boolean'
      ? this.canCancel
        ? ['escape', 'x', 'outside', 'button']
        : []
      : this.canCancel
  }

  get showClose() {
    return this.cancelOptions.indexOf('x') >= 0
  }

  cancel(method: string) {
    if (this.cancelOptions.indexOf(method) < 0) {
      return
    }
    // @ts-ignore
    this.onCancel.apply(null, arguments)
    this.close()
  }

  close() {
    this.$emit('close')
    this.$emit('update:active', false)

    if (this.programmatic) {
      this.isActive = false
      setTimeout(() => {
        this.$destroy()
        removeElement(this.$el)
      }, 150)
    }
  }

  keyPress(event: KeyboardEvent) {
    if (this.isActive && event.keyCode === 27) {
      this.cancel('escape')
    }
  }

  created() {
    if (typeof window !== 'undefined') {
      document.addEventListener('keyup', this.keyPress)
    }
  }

  beforeMount() {
    this.programmatic && document.body.appendChild(this.$el)
  }

  beforeDestroy() {
    if (typeof window !== 'undefined') {
      document.removeEventListener('keyup', this.keyPress)
    }
  }

  mounted() {
    if (this.programmatic) {
      this.isActive = true
    }
  }
}
</script>

<style scoped lang="sass">
  .modal-wrapper
    position: fixed
    top: 0
    bottom: 0
    right: 0
    left: 0
    display: none
    align-items: center
    justify-content: center
    z-index: 50

    &.is-active
      display: flex

  .modal-overlay
    position: absolute
    top: 0
    bottom: 0
    right: 0
    left: 0
    z-index: 100
    background-color: rgba(#000, 0.8)

  .modal-content
    z-index: 100
    max-height: 90vh
    overflow: auto
    position: relative
</style>
