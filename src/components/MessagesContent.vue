<template>
    <el-scrollbar ref="messageContainerRef" always>
        <div ref="innerRef">
            <div v-for="item in messageList" >
                <Message ref="message" :message="item"></Message>
            </div>
        </div>
    </el-scrollbar>
</template>

<script lang="ts" setup>
import Message from './Message.vue'
import {ref, defineExpose, nextTick} from "vue";

defineProps({
    messageList: {type: Array, default: []},
})

const messageContainerRef = ref(null)
const innerRef = ref(null)

const setScrollDown = () => {
    nextTick(() => {
        messageContainerRef.value.setScrollTop(innerRef.value.clientHeight)
    })
}

defineExpose({
    setScrollDown
})
//
// export default {
//     name: "MessagesContent",
//     props: {
//         messageList: {
//             type: Array,
//             default: []
//         }
//     },
//     components: { Message },
//     methods: {
//         scrollDown() {
//             var container= document.getElementById('#innerbox')
//             container.scrollTop = container.scrollHeight
//             // console.log(this.$refs.innerbox.clientHeight)
//             // this.$refs.messageContainerRef.scrollTo({top: 500, behavior: 'smooth'})
//             // this.$refs['messageContainerRef'].wrap.scrollTop = this.$refs['messageContainerRef'].wrap.scrollHeight
//         }
//     }
// }
</script>

<style scoped>

</style>