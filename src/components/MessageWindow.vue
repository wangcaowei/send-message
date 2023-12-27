<template>
  <div class="message-window">
    <div class="header">
      <UserInfo :userInfo="userInfo"/>
    </div>
    <div class="msg-list">
      <MessageList :msgListData="msgListData"/>
    </div>
    <div class="send-msg">
      <SendMessage @sendMsg="setMsgListData"/>
    </div>
  </div>
</template>

<script>
import imgUrl from '@/assets/avator.png'
import UserInfo from './UserInfo.vue'
import MessageList from './MessageList.vue'
import SendMessage from './SendMessage.vue'

export default {
  name: 'MessageWindow',
  components:{
    MessageList,
    SendMessage,
    UserInfo
  },
  data(){
    return {
      userInfo: {
        name: '王小懒',
        imgUrl
      },
      msgListData:[]
    }
  },
  methods:{
    setMsgListData(obj){
      this.msgListData.push(obj)
      console.log('msgListData', this.msgListData)
    }
  }
}
</script>
<style scoped>
.message-window {
  width: 680px;
  height: 100vh;
  margin: auto;
  padding: 10px;
  background-color: #eee;
  display: flex;
  flex-direction: column;
  box-sizing: border-box;
}

.message-window .header {
  height: 30px;
  line-height: 30px;
  border-bottom: 1px solid #333;
}

.msg-list {
  flex: 1;
  overflow-y: auto;
  padding: 10px 0;
  box-sizing: border-box;
}

.send-msg {
  border-top: 1px solid #333;
  height: 260px;
  padding: 10px 0;
  overflow-y: auto;
  box-sizing: border-box;
}
</style>
