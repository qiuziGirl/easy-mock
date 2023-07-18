<template>
  <div class="em-profile">
    <em-header
      icon="edit"
      :title="$t('p.profile.header.title')"
      :description="$t('p.profile.header.description')"
    />
    <em-keyboard-short />
    <Modal v-model="visible" :title="$t('p.profile.modal.title')" width="400">
      <img v-show="form.headImg" :src="form.headImg" style="width: 100%">
    </Modal>
    <transition name="fade">
      <div v-show="pageAnimated" class="em-container">
        <div class="em-profile__content">
          <Row :gutter="20">
            <Col span="18">
              <Form
                ref="form"
                label-position="top"
                :model="form"
                :rules="rules"
              >
                <Form-item :label="$t('p.profile.form.language')">
                  <Select v-model="language">
                    <Option v-for="item in languageList" :key="item.value" :value="item.value">
                      {{ item.label }}
                    </Option>
                  </Select>
                </Form-item>
                <Form-item :label="$t('p.profile.form.nickName')">
                  <i-input v-model="form.nickName" />
                </Form-item>
                <Form-item v-show="!ldap" :label="$t('p.profile.form.password')">
                  <i-input v-model="form.password" type="password" />
                </Form-item>
                <Form-item v-show="!ldap" :label="$t('p.profile.form.passwordCheck')" prop="passwordCheck">
                  <i-input v-model="form.passwordCheck" type="password" />
                </Form-item>
                <Form-item>
                  <Button type="primary" @click="update">
                    {{ $t('p.profile.form.update') }}
                  </Button>
                </Form-item>
              </Form>
            </Col>
            <Col span="6">
              <p>{{ $t('p.profile.form.avatar') }}</p>
              <img
                v-show="form.headImg"
                class="avatar"
                :src="form.headImg"
                :alt="form.nickName"
                :title="form.nickName"
                @click="visible = true"
              >
              <Upload
                :show-upload-list="false"
                :format="['jpg','jpeg','png']"
                :on-success="handleSuccess"
                :headers="uploadHeaders"
                :on-format-error="handleFormatError"
                :action="uploadAPI"
              >
                <Button type="ghost" icon="ios-cloud-upload-outline" long>
                  {{ $t('p.profile.form.upload') }}
                </Button>
              </Upload>
            </Col>
          </Row>
        </div>
      </div>
    </transition>
  </div>
</template>

<style>
@import './index.css';
</style>

<script>
import config from 'config'
import * as api from '../../api'
import languageMap from '../../locale/map'

export default {
  name: 'Profile',
  data () {
    const validatePassCheck = (rule, value, callback) => {
      if (value !== this.form.password) {
        callback(new Error(this.$t('p.profile.validateError')))
      } else {
        callback()
      }
    }

    return {
      ldap: config.ldap,
      visible: false,
      language: this.$ls.get('locale') || 'zh-CN',
      languageList: languageMap.list,
      uploadAPI: '/api/upload',
      form: {
        headImg: this.$store.state.user.headImg,
        nickName: this.$store.state.user.nickName,
        password: '',
        passwordCheck: ''
      },
      rules: {
        passwordCheck: [
          { trigger: 'blur', validator: validatePassCheck }
        ]
      }
    }
  },
  computed: {
    uploadHeaders () {
      return {
        Authorization: 'Bearer ' + this.$store.state.user.token
      }
    }
  },
  methods: {
    handleFormatError (file) {
      this.$Notice.warning({
        title: this.$tc('p.profile.formatError', 1),
        desc: this.$tc('p.profile.formatError', 2, { name: file.name })
      })
    },
    handleSuccess (response) {
      this.form.headImg = response.data.path
    },
    update () {
      const data = {
        nick_name: this.form.nickName,
        head_img: this.form.headImg
      }

      if (this.form.password) {
        data.password = this.form.password
      }

      this.$refs.form.validate((valid) => {
        if (valid) {
          api.u.update({ data }).then((res) => {
            if (res.data.success) {
              this.$ls.set('locale', this.language)
              this.$i18n.locale = this.language
              this.$Modal.success({
                title: this.$tc('p.profile.updateSuccess', 1),
                content: this.$tc('p.profile.updateSuccess', 2),
                onOk: () => {
                  this.$router.push('/log-out')
                }
              })
            }
          })
        }
      })
    }
  }
}
</script>
