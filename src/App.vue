<script setup>
import { ref, onMounted } from 'vue'
const transcript = ref('')
const isRecording = ref(false)

//try to use speech recognition first and if not available then use webkit
const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition
const sr = new Recognition()

//only when application mounted do these run
onMounted(() => {
  //record until stop
    sr.continuous = true;
  //get results every time set
    sr.interimResults = true;

    //checks if started (doesn't actually start)
    sr.onstart = () => {
        console.log("VR started")
        isRecording.value = true;
    } 

    sr.onend = () => {
      console.log("VR ended")
      isRecording.value = false;
      }


      sr.onresult = (evt) => {
        //list needs to be converted to an array
        //whatever the first result passes back we will map to transcript
        const t = Array.from(evt.results)
        //the first result will be the one you are looking for
          .map(result => result[0])
          .map(result => result.transcript)
          .join('')

          transcript.value = t;
      }

  })
//when you hit the button it will turn off if on and on if off
const ToggleMic = () => {
  if(isRecording.value) {
    sr.stop()
    } else {
      sr.start()
    }
}
</script>

<template>
  <div class="app">
    <button :class="'mic'" @click="ToggleMic">Record</button>
    <div class="transcript" v-text="transcript"></div>
  </div>
</template>

<style>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Fire sans', sans-serif;
  }

body {
  background: #261238;
  color: white
  }
</style>
