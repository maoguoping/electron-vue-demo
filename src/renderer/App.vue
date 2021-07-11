<template >
  <div class="app">
    <navigator />
    <div class="update-message">
      更新文本：{{updateText}},下载进度{{progress}}%,在更新{{isUpdateNow}}
    </div>
    <router-view> </router-view>
  </div>
</template>

<script lang=ts>
import { defineComponent, reactive, toRefs, onBeforeUnmount } from 'vue'
import { useIpc } from './composables'
import Navigator from '/@/components/HomeNavigator.vue'

export default defineComponent({
  setup() {
    const { on, removeAllListeners, send } = useIpc()
    const data = reactive({
      updateText: '',
      progress: 0,
      isUpdateNow: false
    })
    on('message', (event, text) => {
      console.log(event, text)
      data.updateText = text
    })
    on('downloadProgress', (event, progressObj) => {
      console.log(event, progressObj)
      data.progress = progressObj.percent || 0
    })
    on('isUpdateNow', (event) => {
      console.log(event)
      data.isUpdateNow = true
      send('isUpdateNow')
    })
    // 发送检查更新
    send('checkForUpdate')
    onBeforeUnmount(() => {
      removeAllListeners('message')
      removeAllListeners('downloadProgress')
      removeAllListeners('isUpdateNow')
    })
    return {
      ...toRefs(data)
    }
  },
  components: {
    Navigator
  }
})
</script>

<style>
  .update-message {
    display: flex;
    flex-direction: row;
    height: 40px;
    line-height: 40px;
    border-bottom: 1px solid #ccc;
  }
</style>
