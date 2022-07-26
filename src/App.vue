<script setup>
import { reactive, onMounted } from 'vue'

const state = reactive({
  websocketUrl:
    'wss://demo.piesocket.com/v3/channel_1?api_key=VCXCEuvhGcBDP7XhiJJUDvR1e1D3eiVjgZ9VRiaV&notify_self',
  responses: [],
  socketConnected: false,
})

let socket = null

const connect = () => {
  socket = new WebSocket(state.websocketUrl)

  socket.onmessage = (event) => {
    state.responses.push(event.data)
    state.socketConnected = true
  }

  socket.onerror = (error) => {
    console.error(error)
  }

  socket.onclose = () => {
    console.log('WebSocket closed')
    state.socketConnected = false
  }
}

const close = () => {
  socket.close()
}

const onSendMessage = (e) => {
  socket.send(e.target.value)
  e.target.value = ''
}
</script>

<template>
  <div class="websocket">
    <div class="info">
      <h3>
        You can get the wss url you will need from
        <a href="https://www.piesocket.com/websocket-tester">piesocket.com</a>
      </h3>
    </div>

    <div class="input-with-button">
      <input v-model="state.websocketUrl" placeholder="Enter websocket url" type="text" />
      <button @click="connect">Connect!</button>
    </div>

    <div class="board">
      <ul>
        <li v-for="res in state.responses" :key="res">{{ res }}</li>
      </ul>
    </div>
    <input
      v-if="state.socketConnected"
      style="width: 95%"
      @keypress.enter="onSendMessage"
      placeholder="Write a message"
      type="text"
    />

    <button @click="close">Close Connection</button>
  </div>
</template>

<style scoped>
.input-with-button {
  display: grid;
  grid-template-columns: 5fr 1fr;
  gap: 0.5em;
  margin-bottom: 1em;
}

input {
  padding: 1em;
}

.board {
  padding: 1em;
  height: 600px;
  overflow-y: scroll;
  border: 1px solid #ccc;
}

ul {
  list-style: none;
  padding: 0;
  margin: 0;
}
</style>
