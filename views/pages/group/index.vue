<template>
  <div class="em-group">
    <em-add
      icon="person-add"
      color="red"
      :bottom="90"
      @click.native="openModal"
    />
    <div v-shortkey="['ctrl', 'c']" @shortkey="openModal" />
    <em-keyboard-short v-model="keyboards" />
    <Modal
      v-model="modalShow"
      class-name="em-group-modal"
      :closable="false"
      @on-ok="submit"
    >
      <Tabs v-model="tabName">
        <Tab-pane
          :label="$tc('p.group.modal.tab.create', 0)"
          name="create"
          :disabled="tabName === 'rename'"
        >
          <Form :label-width="64" @submit.native.prevent>
            <Form-item :label="$tc('p.group.modal.tab.create', 1)">
              <i-input
                ref="inputCreate"
                v-model="groupName"
                :placeholder="$tc('p.group.modal.tab.create', 2)"
                @on-enter="submit"
              />
            </Form-item>
          </Form>
        </Tab-pane>
        <Tab-pane
          :label="$tc('p.group.modal.tab.join', 0)"
          name="join"
          :disabled="tabName === 'rename'"
        >
          <Form :label-width="64" @submit.native.prevent>
            <Form-item :label="$tc('p.group.modal.tab.join', 1)">
              <i-input
                v-model="groupName"
                :placeholder="$tc('p.group.modal.tab.join', 2)"
                @on-enter="submit"
              />
            </Form-item>
          </Form>
        </Tab-pane>
        <Tab-pane
          :label="$tc('p.group.modal.tab.edit', 0)"
          name="rename"
          :disabled="tabName !== 'rename'"
        >
          <Form :label-width="64" @submit.native.prevent>
            <Form-item :label="$tc('p.group.modal.tab.edit', 1)">
              <i-input
                v-model="groupName"
                :placeholder="$tc('p.group.modal.tab.edit', 2)"
                @on-enter="submit"
              />
            </Form-item>
          </Form>
        </Tab-pane>
      </Tabs>
    </Modal>
    <em-placeholder :show="groups.length === 0">
      <Icon :type="keywords ? 'outlet' : 'happy-outline'" />
      <p>{{ keywords ? $tc('p.group.placeholder', 1) : $tc('p.group.placeholder', 2) }}</p>
    </em-placeholder>
    <em-header
      icon="person-stalker"
      :title="$t('p.group.header.title')"
      :description="$t('p.group.header.description')"
    />
    <transition name="fade">
      <div v-show="pageAnimated" class="em-container em-group__list">
        <div class="ivu-row">
          <transition-group name="fadeUp">
            <div
              v-for="item in groups"
              :key="item._id"
              class="ivu-col ivu-col-span-6"
            >
              <div
                class="em-group__item"
                @click="$router.push(`/group/${item._id}?name=${item.name}`)"
              >
                <h2>{{ item.name }}</h2>
                <Button-group class="group-control">
                  <Button type="ghost" icon="edit" @click.stop="rename(item)" />
                  <Button type="ghost" icon="trash-b" @click.stop="remove(item)" />
                </Button-group>
              </div>
            </div>
          </transition-group>
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
import debounce from 'lodash/debounce'

export default {
  name: 'Group',
  asyncData ({ store }) {
    return store.dispatch('group/FETCH')
  },
  data () {
    return {
      groupName: '',
      renameGroup: null,
      modalShow: false,
      tabName: 'create',
      keywords: '',
      keyboards: [
        {
          category: this.$t('p.group.keyboards[0].category'),
          list: [
            {
              description: this.$tc('p.group.keyboards[0].list', 0),
              shorts: ['ctrl', 'c']
            }
          ]
        }
      ]
    }
  },
  computed: {
    groups () {
      const list = this.$store.state.group.list
      const keywords = this.keywords
      return keywords
        ? list.filter(item => new RegExp(keywords, 'i').test(item.name))
        : list
    }
  },
  mounted () {
    this.$on('query', debounce((keywords) => {
      this.keywords = keywords
    }, 500))
  },
  methods: {
    openModal () {
      this.tabName = 'create'
      this.groupName = ''
      this.modalShow = true
      this.$nextTick(() => {
        this.$refs.inputCreate.focus()
      })
    },
    submit () {
      this.modalShow = false
      if (this.tabName === 'create') {
        this.$store.dispatch('group/ADD', this.groupName).then(body => {
          if (body.success) this.$Message.success(this.$t('p.group.create.success'))
        })
      } else if (this.tabName === 'join') {
        this.$store.dispatch('group/JOIN', this.groupName).then(body => {
          if (body.success) {
            this.$Message.success(this.$t('p.group.join.success', { groupName: this.groupName }))
          } else {
            this.$Message.warning(this.$t('p.group.join.warning', { groupName: this.groupName }))
          }
        })
      } else {
        this.$store.dispatch('group/RENAME', {
          id: this.renameGroup._id,
          name: this.groupName
        }).then(body => {
          if (body.success) this.$Message.success(this.$t('p.group.update.success'))
        })
      }
    },
    remove (group) {
      this.$Modal.confirm({
        title: this.$t('confirm.title'),
        content: this.$t('p.group.confirm.delete.content', { name: group.name }),
        onOk: () => {
          this.$store.dispatch('group/REMOVE', group._id).then(body => {
            if (body.success) {
              this.$Message.success(this.$t('p.group.remove.success'))
            }
          })
        }
      })
    },
    rename (group) {
      this.tabName = 'rename'
      this.modalShow = true
      this.groupName = group.name
      this.renameGroup = group
    }
  }
}
</script>

<style>
@import './index.css';
</style>
