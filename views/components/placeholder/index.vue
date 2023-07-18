<template>
  <div class="em-placeholder">
    <transition name="fade">
      <div v-show="visible" class="em-placeholder__content">
        <slot />
      </div>
    </transition>
  </div>
</template>

<style>
@import './index.css';
</style>

<script>
export default {
  name: 'EmPlaceholder',
  props: {
    show: {
      type: Boolean,
      default: false
    }
  },
  data () {
    return {
      visible: false
    }
  },
  watch: {
    show: 'handleVisible'
  },
  mounted () {
    this.handleVisible()
  },
  methods: {
    handleVisible () {
      setTimeout(() => {
        this.visible = this.show
      }, 500)
    }
  }
}
</script>
