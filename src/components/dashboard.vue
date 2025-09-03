<template>
  <div class="app-container">
    <h1 class="title">Sayang GPT</h1>
    <p class="subtitle">Ask my anything sayang</p>
    <div class="input-container">
      <InputTextarea
        v-model="message"
        rows="6"
        cols="60"
        placeholder="Type your message here..."
        autoResize
        class="large-input"
        :disabled="loading"
      />
      <br/>
    </div>
    <div class="input-container2">
      <Button label="Send" @click="sendMessage" class="send-button" />
    </div>
    <ProgressSpinner v-if="loading" />
    <div v-if="aiANswer">
      <Card style="margin-top: 3rem">
        <template #title>{{ tempMsg }}</template>
        <template #content>
          <p class="m-0">
            {{ aiANswer }}
          </p>
        </template>
      </Card>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import InputTextarea from 'primevue/textarea'
import Button from 'primevue/button'
import axios from 'axios'

import ProgressSpinner from 'primevue/progressspinner';


import Card from 'primevue/card'

const message = ref('')
const aiANswer = ref('');
const loading = ref(false);
const tempMsg = ref('');

function sendMessage() {
  if (message.value.trim() !== '') {
     loading.value = true; 
    tempMsg.value = message.value;
    sendQuery()
    message.value = ''
  }
}

const sendQuery = async () => {
  try {
    const response = await axios.post('http://localhost:3000/deepseek', {
      query: message.value,
    })
    console.log('Response from backend>>>>', response.data);
    aiANswer.value = response.data;
   
    aiANswer.value = aiANswer.value
    .replace(/\*\*(.+?)\*\*/g, '<strong>$1</strong>') // bold text
    .replace(/\n/g, '<br>'); // new lines to breaks
  } catch (error) {
    console.error('Error sending query:', error)
    aiANswer.value = 'Sorry, there was an error processing your request.';
  }
  finally {
    loading.value = false;  // hide spinner
  }
}


</script>

<style scoped>
.app-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #e0f7fa, #ffffff);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  padding: 2rem;
  text-align: center;
}

.title {
  font-size: 3rem;
  font-weight: bold;
  margin-bottom: 0.25rem;
  color: #0277bd;
  user-select: none;
}

.subtitle {
  font-size: 1.2rem;
  color: #555;
  margin-bottom: 2rem;
  user-select: none;
}

.input-container {
  display: flex;
  gap: 1rem;
  align-items: center;
  justify-content: center;
}

.input-container2 {
  margin-top: 1rem;
  display: flex;
  margin-left: 17rem;
  
}

.large-input {
  font-size: 1.25rem;
  width: 400px;
  max-width: 90vw;
  padding: 0.75rem;
  border-radius: 0.5rem;
  border: 1px solid #bbb;
  resize: vertical;
  box-shadow: 0 2px 6px rgb(0 0 0 / 0.1);
}

.send-button {
  height: 3.2rem;
  font-size: 1.2rem;
  padding: 0 2rem;
  border-radius: 0.5rem;
}
</style>
