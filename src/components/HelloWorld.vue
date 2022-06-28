<script setup lang="ts">
import {ref} from 'vue'
import socketClient from "socket.io-client";
import ProgressBar from "./ProgressBar.vue";

const SERVER = "http://localhost:3000";
var socket = socketClient(SERVER);
let input = ref("https://youtu.be/ouncVBiye_M")
let progresses = ref<{ [pid: string]: { percent: number, totalSize: string, eta: string, currentSpeed: string, downloadLink?: string } }>({})

const requestDownlaod = () => {
  socket.emit('download-req', {link: input.value});
}

socket.on('download-progress', (data: {
  meta: { pid: number }
  percent: number
  totalSize: string
  eta: string,
  currentSpeed: string
}) => {
  console.log(data)
  progresses.value[data.meta.pid] = {...progresses.value[data.meta.pid], ...data}

});


socket.on('download-done', (data: { link: string, pid: number }) => {
  console.log(data)
  progresses.value[data.pid] = {...progresses.value[data.pid], downloadLink: data.link}
});

</script>

<template>

  <input type="text" v-model="input">
  <button id="send" @click="requestDownlaod">send</button>
  <div id="done"></div>

  <div class="wrapper">
    <template v-for="pid in Object.keys(progresses)" class="progress-container">

      <div>{{ pid }}</div>
      <div>
        <ProgressBar v-bind:percent="progresses[pid].percent"></ProgressBar>
      </div>
      <div>{{ progresses[pid].currentSpeed }}</div>
      <div>{{ progresses[pid].eta }}</div>
      <div>{{ progresses[pid].totalSize }}</div>
      <div><a v-bind:href="progresses[pid].downloadLink">Download</a></div>
    </template>
  </div>
</template>

<style scoped>
.wrapper {
  display: grid;
  grid-template-columns: 15% 15% 15% 15% 15% 15%;
}

.wrapper {
  background: #539ee3;
  color: #0a0a0a;
  border: 3px solid;
  padding: 3px;
  margin: 3px;
}

a {
  color: #42b983;
}

label {
  margin: 0 0.5em;
  font-weight: bold;
}

code {
  background-color: #eee;
  padding: 2px 4px;
  border-radius: 4px;
  color: #304455;
}
</style>
