<script setup lang="ts">
import {ref} from 'vue'
import socketClient from "socket.io-client";
import ProgressBar from "./ProgressBar.vue";

const SERVER = "https://test.mytaskplan.me" // 'http://localhost:3088/';
var socket = socketClient(SERVER);
let input = ref("")
let fileFormat = ref("mp4")
let progresses = ref<{ [pid: string]: { percent: number, totalSize: string, eta: string, currentSpeed: string, downloadLink?: string } }>({})

const requestDownlaod = () => {
  socket.emit('download-req', {link: input.value, format: fileFormat.value});
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

  <div class="title"><b> Youtube downloader </b></div>
  <input type="text" id="link-input" placeholder="put the youtube link here" v-model="input">
  <select class="select-format" name="format" id="cars" v-model="fileFormat">
    <option value="mp4">mp4</option>
    <option value="mp3">mp3</option>
  </select>

  <button id="send-request-btn" @click="requestDownlaod">download</button>
  <div id="done"></div>

  <div class="wrapper">
    <template v-for="pid in Object.keys(progresses)" class="progress-container list-item">
        <div class="element">
          <div>
            {{ pid }}
            <div>
              <video class="embed-video" v-if="progresses[pid].downloadLink"  controls>
                <source v-bind:src="progresses[pid].downloadLink" type="video/mp4">
                Your browser does not support HTML video.
              </video>
            </div>
          </div>
          <div>
            <ProgressBar v-bind:percent="progresses[pid].percent"></ProgressBar>
          </div>
          <div class="center">{{ progresses[pid].currentSpeed }}</div>
          <div>{{ progresses[pid].eta }}</div>
          <div>{{ progresses[pid].totalSize }}</div>
          <div>
            <a  class="download-btn" v-if="progresses[pid].downloadLink" v-bind:href="progresses[pid].downloadLink">Download</a><p></p>
          </div>

        </div>
    </template>
  </div>
</template>

<style scoped lang="scss">

.embed-video {
  padding: 5px;
  margin: 5px;
  width: 80%;
}

.select-format{
  padding: 22px;
  font-size: 16px;
  border-radius: 10px;
  margin: 5px;
}

.download-btn {
  padding: 22px;
  border-radius: 10px;
  background: #BB000E;

  font-size: 16px;
  color: white;
}

#send-request-btn {
  padding: 22px;
  border-radius: 10px;
  background: #BB000E;

  font-size: 16px;
  color: white;
}

#link-input {
  padding: 22px;
  border-radius: 10px;
  width: 50%;
}

.title {
  font-size: 53px;
  color: #BB000E;
  padding: 22px;
}

.element {
  display: grid;
  grid-auto-flow: column;
  gap: 4px;
  align-items: center;
  justify-items: center;
  grid-template-columns: 15% 15% 15% 15% 15% 15%;
  background: #F9E9B8;
  color: #0a0a0a;
  border-radius: 25px;
  padding: 3px;
  margin: 10px;
  font-size: 22px;
}


.wrapper {
  border: 3px solid;
  padding: 3px;
  margin: 10px;
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
