<template>
  <transition name="fade">
    <div
      v-show="pageAnimated"
      class="em-header"
      :class="{'em-header--fixed': isFixed}"
    >
      <Affix @on-change="changeFixed">
        <div class="em-header__content">
          <transition name="fade">
            <em-spots v-if="routeChanged" :size="spots" />
          </transition>
          <div class="em-container">
            <Row>
              <i-col class="logo" span="1">
                <transition name="fade">
                  <Icon v-show="routeChanged" :type="icon" />
                </transition>
              </i-col>
              <i-col span="16" class="em-header__info">
                <transition-group name="fade">
                  <h2 v-show="routeChanged" key="a">
                    {{ title }}
                  </h2>
                  <p v-show="routeChanged" key="b">
                    {{ description }}
                  </p>
                </transition-group>
              </i-col>
              <i-col span="6" class="em-header__control">
                <slot v-if="!nav" />
              </i-col>
            </Row>
          </div>
          <div v-if="nav" class="em-header__nav">
            <div class="em-container">
              <div style="float: right;">
                <div
                  v-for="(item, i) in nav"
                  :key="i"
                  class="em-header__nav__item"
                  :class="{'is-active': value === item.title}"
                  @click="$emit('input', item.title)"
                >
                  <Icon v-if="item.icon" :type="item.icon" /> {{ item.title }}
                </div>
              </div>
            </div>
          </div>
        </div>
      </Affix>
    </div>
  </transition>
</template>

<style>
@import './index.css';
</style>

<script>
export default {
  name: 'EmHeader',
  props: {
    title: String,
    description: String,
    icon: String,
    spots: Number,
    nav: Array,
    value: {}
  },
  data () {
    return {
      routeChanged: true,
      isFixed: false
    }
  },
  watch: {
    title: function () {
      this.routeChanged = false
      this.$nextTick(() => {
        this.routeChanged = true
      })
    }
  },
  methods: {
    changeFixed (isFixed) {
      this.isFixed = isFixed
    }
  }
}
</script>
