<template>
  <div v-show="show" class="em-loading">
    <Spin size="large" fix />
  </div>
</template>

<script>
export default {
  name: 'EmLoading',
  data () {
    return {
      show: false
    }
  },
  mounted () {
    document.addEventListener('scroll', this.scroll)
  },
  beforeDestroy () {
    this.destroy()
  },
  methods: {
    scroll () {
      const scrollTop = document.documentElement.scrollTop ||
                        window.pageYOffset || document.body.scrollTop
      const winHeight = window.innerHeight
      const bodyHeight = document.body.clientHeight

      if (scrollTop + winHeight === bodyHeight) {
        this.show = true
        this.$emit('loading')
      }
    },
    stop () {
      this.show = false
    },
    destroy () {
      this.stop()
      document.removeEventListener('scroll', this.scroll)
    }
  }
}
</script>

<style>
.em-loading {
  position: relative;
  margin-top: 20px;
}

.em-loading .ivu-spin-fix {
  background: transparent;
  padding-bottom: 20px;
}
</style>
