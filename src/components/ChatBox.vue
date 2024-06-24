<script setup lang="ts">
import { ref } from 'vue';
import ollama from 'ollama';

const chatInput = ref('Why is the sky blue?')
const messages = ref([{ role: 'agent', content: 'Hello, I am kand7dev. How can I help you today?'}]);
const currentOutputMessageContent = ref('')
const submitChat = async () => {
    const content = chatInput.value;
    chatInput.value = '';
    const inputMessage = { role: 'user', content };
    messages.value.push(inputMessage);
    const response = await ollama.chat({
        model: 'phi3',
        messages: [inputMessage],
        stream: true
    });
    for await (const part of response){
        currentOutputMessageContent.value += part.message.content
    }
    messages.value.push({ role: 'agent', content: currentOutputMessageContent.value })
    currentOutputMessageContent.value = ''
    }

</script>

<template>
    <div id ="chatBox">
        <div id="chatContainer">
            <div id="chatArea" ref="chatArea"></div>
            <div v-for="message in messages" :key="message.content">
                {{ message.content }}
            </div>
            <div v-if="currentOutputMessageContent">
                {{  currentOutputMessageContent }}
            </div>
        </div>
        <div id="inputArea">
            <input id="chatInput" v-model="chatInput" @keyup.enter="submitChat">
            <button @click="submitChat" id="submitButton">Submit</button>
        </div>
    </div>
</template>

<style scoped> 
#chatBox {
    display: flex;
    height: 100%;
    flex-direction: column;
}

#chatContainer {
    position: relative;
    width: 100%;
    height: calc(100% - 100px);
}

#chatArea { 
    position: absolute;
    top: 0;
    left: 0;
    bottom: 0;
    right: 0;
    overflow-y: auto;
}
</style>