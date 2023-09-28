<template>
    <div id="user-manager">
        <el-container style="height: 100%">
            <el-aside id="user-aside" width="400px">
                <el-row>
                    <el-col :span="6">
                        <el-avatar :size="70" src="https://cdn.icon-icons.com/icons2/2438/PNG/512/boy_avatar_icon_148455.png"></el-avatar>
                    </el-col>
                    <el-col :span="18">
                        <h3>{{character.char_name || "请选择一个Character"}}</h3>
                    </el-col>
                </el-row>
                <br />
                <el-input  placeholder="Character Name" v-model="character.char_name"></el-input>
                <br /><br />
                <el-input type="textarea" :rows="10" placeholder="Character Desc" v-model="character.desc"></el-input>
                <br /><br />
                <el-input type="textarea" :rows="10" placeholder="Greeting" v-model="character.greeting"></el-input>
                <br /><br />
                <el-space fill style="width: 100%">
                    <el-button type="primary">Save Character</el-button>
                </el-space>

                <el-divider />
                <h3>Parameters</h3>
                <div class="slider-demo-block">
                    <span class="demonstration">Temperature</span>
                    <el-slider v-model="deContent" />
                </div>
                <div class="slider-demo-block">
                    <span class="demonstration">Top_p</span>
                    <el-slider v-model="deContent" />
                </div>
                <div class="slider-demo-block">
                    <span class="demonstration">Top_k</span>
                    <el-slider v-model="deContent" :step="10" />
                </div>
            </el-aside>
            <el-container>
                <el-header>
                    <el-menu
                        :default-active="activeIndex"
                        class="el-menu-demo"
                        mode="horizontal"
                        :ellipsis="false"
                        @select="handleSelect"
                    >
                        <el-menu-item index="0">APP</el-menu-item>
                        <div class="flex-grow" />
                        <el-menu-item index="1">Create Character</el-menu-item>
                        <el-sub-menu index="2">
                            <template #title>Characters</template>
                                <el-menu-item v-for="(item,index) in characters" :key="index" :index="item">{{ item }}</el-menu-item>
<!--                            <el-menu-item index="2-1">Character one</el-menu-item>-->
<!--                            <el-menu-item index="2-2">Character two</el-menu-item>-->
<!--                            <el-menu-item index="2-3">Character three</el-menu-item>-->
<!--                            <el-sub-menu index="2-4">-->
<!--                                <template #title>item four</template>-->
<!--                                <el-menu-item index="2-4-1">item one</el-menu-item>-->
<!--                                <el-menu-item index="2-4-2">item two</el-menu-item>-->
<!--                                <el-menu-item index="2-4-3">item three</el-menu-item>-->
<!--                            </el-sub-menu>-->
                        </el-sub-menu>
                    </el-menu>
                </el-header>
                <el-main id="user-main">
                    <MessagesContent ref="messagesContent" :message-list="messageList"></MessagesContent>
                </el-main>
                <el-footer id="user-footer">
                    <el-row :gutter="10" align="middle">
                        <el-col :span="20">
                            <el-input v-model="sendContent" class="send-input" placeholder="Input Content" @keyup.enter.native="handleSend"/>
                        </el-col>
                        <el-col :span="4">
                            <el-button class="send-button" type="primary" @click="handleSend">Send</el-button>
                        </el-col>
                    </el-row>
                </el-footer>
            </el-container>
        </el-container>
    </div>
</template>

<script setup>
import { ref,reactive } from 'vue'
import axios from 'axios';
import MessagesContent from "./MessagesContent.vue";

const character = reactive({
    char_name: "未知AI",
    desc: "NONE",
    greeting: "NONE"
})

const messageList = ref([
    {
        username: character.char_name,
        message: character.greeting
    }
])
const playerName = ref("Eric Ball").value
const history = ref("")

const activeIndex = ref('Jaxon Brown.json').value
const characters = ref([])
axios.get("/api/characters").then((res)=> {
    if(res.status === 200){
        characters.value = res.data.data
    }
})


const sendContent = ref('')
const deContent = ref('')

const handleSelect = (key, keyPath) => {
    getCharacterInfo(key)
}

const getCharacterInfo = async (key) => {
    axios.get("/api/characters/"+key).then((res)=> {
        if(res.status === 200){
            const data = res.data.data
            character.char_name = data["char_name"];
            character.desc = data["char_persona"]+"\n"+data["task_info"]+"\n"+data["world_scenario"];
            character.greeting = "HI ";
            history.value = ""
            messageList.value = [
                {
                    username: character.char_name,
                    message: character.greeting
                }
            ]

        }
    })
}
getCharacterInfo(activeIndex)

/**
 * 发送AI聊天
 * @type {Ref<any>}
 */
const messagesContent = ref()
const handleSend = async () => {
    if(history.value === ""){
        history.value = "<START>\n" + character.char_name + ": " + character.greeting + "\n"
    }
    messageList.value.push({username: playerName, message: sendContent.value, avatar: "https://cdn.icon-icons.com/icons2/2438/PNG/512/boy_avatar_icon_148455.png"})
    messagesContent.value.setScrollDown()
    const userInput = sendContent.value;
    history.value += playerName + ": " + userInput + "\n"
    const request = {
        "player_name": playerName,
        "char_name": character.char_name,
        "description": character.desc,
        "greeting_message": character.greeting,
        "user_input": userInput,
        "history": history.value
    }
    sendContent.value = ""
    axios.post("/api/generate",request).then((res)=> {
        if(res.status === 200){
            const data = res.data.data
            history.value += character.char_name + ": " + data + "\n"
            messageList.value.push({username: character.char_name, message: data, avatar: "https://cdn.icon-icons.com/icons2/2438/PNG/512/boy_avatar_icon_148455.png"})
            messagesContent.value.setScrollDown()
        }
    })
}





</script>

<style lang="scss" scoped>
.flex-grow {
    flex-grow: 1;
}
#user-manager{
    height: 100%;
}

#user-aside{
    padding: 2rem;
}

#user-main{
    margin: 10px;
}

#user-footer{
    //height: 300px;
    $send-height: 50px;
    .send-input{
        height: $send-height;
        width: 100%;
    }
    .send-button{
        height: $send-height;
        width: 100%;
    }
}

</style>