<template>
  <m-popup :ok-text="$t('Rename')" :nameText="$t('Rename')" @ok="_ok" :asyn-loading="true">
    <template slot="content">
      <div class="resource-rename-model">
        <m-list-box-f>
          <template slot="name"><b>*</b>{{$t('Name')}}</template>
          <template slot="content">
            <x-input
                    type="input"
                    v-model="name"
                    :placeholder="$t('Please enter name')"
                    autocomplete="off">
            </x-input>
          </template>
        </m-list-box-f>
        <m-list-box-f>
          <template slot="name">{{$t('Description')}}</template>
          <template slot="content">
            <x-input
                    type="textarea"
                    v-model="desc"
                    :placeholder="$t('Please enter description')"
                    autocomplete="off">
            </x-input>
          </template>
        </m-list-box-f>
      </div>
    </template>
  </m-popup>
</template>
<script>
  import i18n from '@/module/i18n'
  import store from '@/conf/home/store'
  import mPopup from '@/module/components/popup/popup'
  import mListBoxF from '@/module/components/listBoxF/listBoxF'

  export default {
    name: 'resource-file-rename',
    data () {
      return {
        store,
        desc: '',
        name: ''
      }
    },
    props: {
      item: Object
    },
    methods: {
      _ok (fn) {
        this._verification().then(res => {
          if (this.name === this.item.alias) {
            return new Promise((resolve,reject) => {
              this.desc === this.item.desc ? reject({msg:'内容未修改'}) : resolve()
            })
          }else{
            return this.store.dispatch('resource/resourceVerifyName', {
              name: this.name,
              type: 'FILE'
            })
          }
        }).then(res => {
          return this.store.dispatch('resource/resourceRename', {
            name: this.name,
            desc: this.desc,
            id: this.item.id,
            type: 'FILE'
          })
        }).then(res => {
          this.$message.success(res.msg)
          this.$emit('onUpDate', res.data)
          fn()
        }).catch(e => {
          fn()
          this.$message.error(e.msg || '')
        })
      },
      _verification () {
        return new Promise((resolve, reject) => {
          if (!this.name) {
            reject({ // eslint-disable-line
              msg: `${i18n.$t('Please enter resource name')}`
            })
          } else {
            resolve()
          }

        })
      }
    },
    watch: {},
    created () {
      let item = this.item || {}
      if (item) {
        this.name = item.alias
        this.desc = item.desc
      }
    },
    mounted () {
    },
    components: { mPopup, mListBoxF }
  }
</script>