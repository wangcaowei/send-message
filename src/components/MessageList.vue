<template>
    <div class="message-list">
      <div class="message-info" v-for="(item,index) in msgListData" :key="index">
        <img src="../assets/avator.png" alt="" class="avator">
        <el-popover placement="right" :content="item.date" trigger="hover">
            <div slot="reference" v-html="item.msg" class="msg-text"></div>
        </el-popover>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'MessageList',
    props: {
        msgListData:{
            type: Array,
            default: () => ([]),
        }
    }
  }
  </script>
  
  <style scoped lang="scss">
  .message-list {
    font-size: 16px;
  }
  .message-info {
    display: flex;
    margin-bottom: 10px;
  }

  .msg-text {
    max-width: 260px;
    padding: 5px 10px;
    background-color: #fff;
    border-radius: 5px;

    ::v-deep {
        img {
            max-width: 240px !important;
        }
    }
  }

  .avator {
    width: 24px;
    height: 24px;
    border-radius: 50%;
    margin-right: 10px;
  }
  </style>
  