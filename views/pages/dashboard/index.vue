<template>
  <div class="em-dashboard">
    <em-header
      :spots="6"
      icon="ios-speedometer"
      :title="$t('p.dashboard.header.title')"
      :description="$t('p.dashboard.header.description')"
    />
    <em-keyboard-short />
    <div class="em-container em-dashboard__content">
      <Row :gutter="20">
        <Col span="12">
          <transition name="fadeLeft">
            <div
              v-show="pageAnimated"
              class="em-dashboard__item em-dashboard__item--key"
            >
              <h2>
                <Icon type="stats-bars" />
                {{ $tc('p.dashboard.total.mockUse', 1) }}
              </h2>
              <p class="number">
                <em-animated-integer :value="total.mockUseCount" />
                <span>{{ $tc('p.dashboard.total.mockUse', 2) }}</span>
              </p>
            </div>
          </transition>
        </Col>
        <Col span="6">
          <transition name="fadeRight">
            <div v-show="pageAnimated" class="em-dashboard__item">
              <h2>
                <Icon type="cube" />
                {{ $tc('p.dashboard.total.project', 1) }}
              </h2>
              <p class="number">
                <em-animated-integer :value="total.projectCount" />
                <span>{{ $tc('p.dashboard.total.project', 2) }}</span>
              </p>
            </div>
          </transition>
        </Col>
        <Col span="6">
          <transition name="fadeRight">
            <div v-show="pageAnimated" class="em-dashboard__item">
              <h2>
                <Icon type="link" />
                {{ $tc('p.dashboard.total.mock', 1) }}
              </h2>
              <p class="number">
                <em-animated-integer :value="total.mockCount" />
                <span>{{ $tc('p.dashboard.total.mock', 2) }}</span>
              </p>
            </div>
          </transition>
        </Col>
      </Row>
      <Row :gutter="20">
        <Col span="12">
          <transition name="fadeLeft">
            <div v-show="pageAnimated" class="em-dashboard__item em-dashboard__item--key">
              <em-spots :size="6" />
              <h2>
                <Icon type="person" />
                {{ $tc('p.dashboard.total.user', 1) }}
              </h2>
              <p class="number">
                <em-animated-integer :value="total.userCount" />
                <span>{{ $tc('p.dashboard.total.user', 2) }}</span>
              </p>
            </div>
          </transition>
        </Col>
        <Col span="12">
          <transition name="fadeRight">
            <div v-show="pageAnimated" class="em-dashboard__item">
              <em-spots :size="6" />
              <h2>
                <Icon type="person-add" />
                {{ $tc('p.dashboard.today.user', 1) }}
              </h2>
              <p class="number">
                <em-animated-integer :value="today.userCount" />
                <span>{{ $tc('p.dashboard.today.user', 2) }}</span>
              </p>
            </div>
          </transition>
        </Col>
      </Row>
      <Row :gutter="20">
        <Col span="12">
          <transition name="fadeLeft">
            <div v-show="pageAnimated" class="em-dashboard__item em-dashboard__item--key">
              <h2>
                <Icon type="stats-bars" />
                {{ $tc('p.dashboard.today.mockUse', 1) }}
              </h2>
              <p class="number">
                <em-animated-integer :value="today.mockUseCount" />
                <span>{{ $tc('p.dashboard.today.mockUse', 2) }}</span>
              </p>
            </div>
          </transition>
        </Col>
        <Col span="6">
          <transition name="fadeRight">
            <div v-show="pageAnimated" class="em-dashboard__item">
              <h2>
                <Icon type="cube" />
                {{ $tc('p.dashboard.today.project', 1) }}
              </h2>
              <p class="number">
                <em-animated-integer :value="today.projectCount" />
                <span>{{ $tc('p.dashboard.today.project', 2) }}</span>
              </p>
            </div>
          </transition>
        </Col>
        <Col span="6">
          <transition name="fadeRight">
            <div v-show="pageAnimated" class="em-dashboard__item">
              <h2>
                <Icon type="link" />
                {{ $tc('p.dashboard.today.mock', 1) }}
              </h2>
              <p class="number">
                <em-animated-integer :value="today.mockCount" />
                <span>{{ $tc('p.dashboard.today.mock', 2) }}</span>
              </p>
            </div>
          </transition>
        </Col>
      </Row>
      <transition name="fadeUp">
        <div v-show="pageAnimated" class="em-dashboard__users">
          <Row>
            <i-col span="6">
              <em-spots :size="6" />
              <div class="em-dashboard__users__title">
                <Icon type="quote" />
              </div>
            </i-col>
            <i-col span="18">
              <Row :gutter="10" style="padding: 0 10px;">
                <i-col v-for="(item, i) in users" :key="i" span="2">
                  <img :src="item.head_img" :title="item.nick_name">
                </i-col>
              </Row>
            </i-col>
          </Row>
        </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Dashboard',
  asyncData ({ store }) {
    return store.dispatch('dashboard/FETCH')
  },
  computed: {
    total () {
      return this.$store.state.dashboard.total
    },
    today () {
      return this.$store.state.dashboard.today
    },
    users () {
      return this.$store.state.dashboard.users
    }
  }
}
</script>

<style>
@import './index.css';
</style>
