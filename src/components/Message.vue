<template>
  <div id="user-manager">
      <el-row align="middle">
          <el-col :span="2">
              <el-avatar :size="60" :src="message.avatar"></el-avatar>
          </el-col>
          <el-col :span="22">
              <div class="user-name">{{ message.username }}</div>
              <div class="chat-content">{{ message.message || "Hello" }}</div>
              <div class="user-feedback">
                  <div class="feedback-content">觉得这个回复怎么样~</div>
                  <el-space>
<!--                      <el-button :icon="Check" size="small" type="primary" />-->
<!--                      <el-divider direction="vertical"></el-divider>-->
                      <el-button :icon="Refresh" size="small" />
                      <el-button :icon="EditPen" size="small" type="info" />
                      <el-button :icon="Delete" size="small" type="danger" />
                  </el-space>
              </div>
          </el-col>
      </el-row>
      <el-divider />
  </div>
  <br />
</template>

<script setup>
import { Check, Refresh, EditPen, Delete } from '@element-plus/icons-vue'
import {onMounted} from "vue";
defineProps({
    message: {type: Object, default: {avatar: "https://cdn.icon-icons.com/icons2/2438/PNG/512/boy_avatar_icon_148455.png", message: "未知消息", username: "未知用户" }},
})
</script>

<style lang="scss" scoped>
#user-manager{
    .user-name{
        margin-bottom: 5px;
    }

    .chat-content{
        width: 100%;
        word-wrap:break-word;
        color: #888888;
        margin-bottom: 10px;
    }

    .feedback-content{
        font-size: 13px;
        color: cornflowerblue;
        margin-bottom: 5px;
    }
}
</style>