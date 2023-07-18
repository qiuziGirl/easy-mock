<template>
  <div class="em-layout">
    <em-add @click.native="$router.push('/new')" />
    <div v-shortkey="['p']" @shortkey="$router.push('/')" />
    <div v-shortkey="['g']" @shortkey="$router.push('/group')" />
    <div v-shortkey="['w']" @shortkey="$router.push('/workbench')" />
    <div v-shortkey="['d']" @shortkey="$router.push('/docs')" />
    <div v-shortkey="['n']" @shortkey="$router.push('/new')" />
    <div v-shortkey="['s']" @shortkey="onSearch" />

    <transition name="fade">
      <div v-show="pageAnimated" class="em-layout__nav">
        <Menu theme="dark" :active-name="pageKey" mode="horizontal">
          <div class="nav-logo" @click="$router.push('/')">
            <img src="/public/images/easy-mock.png">
          </div>
          <div class="nav-search">
            <i-input ref="search" v-model="searchValue" placeholder="Search Easy Mock" />
          </div>
          <Submenu name="1">
            <template slot="title">
              <Icon type="pound" /> {{ $t('c.layout.menu[0][0]') }}
            </template>
            <Menu-item
              name="/"
              @click.native="$router.push('/')"
            >
              <Icon type="person" /> {{ $t('c.layout.menu[0][1]') }}
            </Menu-item>
            <Menu-item
              name="/group"
              @click.native="$router.push('/group')"
            >
              <Icon type="person-stalker" /> {{ $t('c.layout.menu[0][2]') }}
            </Menu-item>
          </Submenu>
          <Menu-item
            name="/workbench"
            @click.native="$router.push('/workbench')"
          >
            <Icon type="code-working" /> {{ $t('c.layout.menu[1]') }}
          </Menu-item>
          <Menu-item
            name="/dashboard"
            @click.native="$router.push('/dashboard')"
          >
            <Icon type="ios-speedometer" /> {{ $t('c.layout.menu[2]') }}
          </Menu-item>
          <Menu-item
            name="/docs"
            @click.native="$router.push('/docs')"
          >
            <Badge dot :count="readChangelog ? '0' : '1'">
              <Icon type="ios-book" /> {{ $t('c.layout.menu[3]') }}
            </Badge>
          </Menu-item>
          <Submenu name="100">
            <template slot="title">
              <Icon type="egg" /> {{ $t('c.layout.menu[4][0]') }}
            </template>
            <li
              class="ivu-menu-item"
              @click="open('https://github.com/easy-mock/easy-mock')"
            >
              <Icon type="link" /> GitHub
            </li>
            <li
              class="ivu-menu-item"
              @click="open('https://github.com/easy-mock/easy-mock-cli')"
            >
              <Icon type="link" /> {{ $t('c.layout.menu[4][1]') }}
            </li>
            <li
              class="ivu-menu-item"
              @click="open('http://mockjs.com/examples.html')"
            >
              <Icon type="link" /> {{ $t('c.layout.menu[4][2]') }}
            </li>
          </Submenu>
          <Submenu v-show="userHeadImg" name="5" class="nav-avatar">
            <template slot="title">
              <img v-show="userHeadImg" :src="userHeadImg">
            </template>
            <Menu-item
              name="/profile"
              @click.native="$router.push('/profile')"
            >
              <Icon type="edit" /> {{ $t('c.layout.menu[5][0]') }}
            </Menu-item>
            <Menu-item
              name="/log-out"
              @click.native="logOut"
            >
              <Icon type="log-out" /> {{ $t('c.layout.menu[5][1]') }}
            </Menu-item>
          </Submenu>
          <Menu-item
            v-show="!userHeadImg"
            class="nav-avatar"
            name="/login"
            @click.native="$router.push('/login')"
          >
            <Icon type="log-in" /> {{ $t('c.layout.menu[5][2]') }}
          </Menu-item>
        </Menu>
      </div>
    </transition>
    <div class="em-layout__content">
      <transition name="fade" mode="out-in">
        <router-view />
      </transition>
    </div>
    <p v-if="copyright && pageAnimated" class="em-layout__copyright">
      {{ copyright }}
    </p>
  </div>
</template>

<style>
@import './index.css';
</style>

<script>
import config from 'config'
import Emitter from '../../mixins/emitter'

export default {
  name: 'EmLayout',
  mixins: [Emitter],
  data () {
    return {
      searchValue: '',
      pageKey: '',
      copyright: config.copyright
    }
  },
  computed: {
    userHeadImg () {
      return this.$store.state.user.headImg
    },
    readChangelog () {
      return this.$store.state.app.readChangelog
    }
  },
  watch: {
    '$route' (to) {
      this.pageKey = to.path
    },
    searchValue: function (value) {
      this.broadcast('group', 'query', value)
      this.broadcast('project', 'query', value)
      this.broadcast('projectDetail', 'query', value)
    }
  },
  mounted () {
    this.pageKey = this.$route.path
    this.show = true
  },
  methods: {
    open (url) {
      window.open(url)
    },
    logOut () {
      this.$router.push('/log-out')
    },
    onSearch () {
      this.$refs.search.focus()
    }
  }
}
</script>
