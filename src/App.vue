<script setup>
import { ref, onMounted } from 'vue'
const transcript = ref('')
const isRecording = ref(false)

const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition
const sr = new Recognition()


onMounted(() =>{
  sr.continuous = true
  sr.interimResults = true

  sr.onstart = () => {
    console.log('SR Started')
    isRecording.value = true
  }

  sr.onend = () => {
    console.log('SR Stopped')
    isRecording.value = false
  }

  sr.onresult = (evt) => {
    for(let i = 0; i < evt.results.length; i++) {
      const result = evt.results[i]
      if(result.isFinal) CheckForCommand(result)
    }
   //console.log(evt)
   const t = Array.from(evt.results)
   .map(result => result[0])
   .map(result => result.transcript)
   .join('')

   transcript.value = t
  };

})

const CheckForCommand = (result) => {
  const t = result[0].transcript;
  if(t.includes('stop recording')) {
    sr.stop()
  } else if (t.includes('what is the time') || t.includes('what\'s the time')) {
      sr.stop()
      alert(new Date().toLocaleTimeString())
      setTimeout(() => sr.start(), 100)
    }
}

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
    <button :class="`mic`" @click="ToggleMic">Record</button>
    <div class="transcript" v-text="transcript"></div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family:'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
}

body {
  background: #09011a;
  color: #fff;
}

.app {
  padding: 10px;
}

.app .transcript {
  padding-top: 10px;
  font-size: 30px;
}

.app button{
  background: #fff;
  border: none;
  border-radius: 10px;
  min-width: 100px;
  min-height: 20px;
  color:#09011a;
  font-weight: 500;
}
</style>
