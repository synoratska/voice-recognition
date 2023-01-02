<script setup>
import { ref, onMounted } from "vue";

const transcript = ref("");
const isRecording = ref(false);

const Recognition = window.SpeechRecognition || window.webkitSpeechRecognition;

const speechRec = new Recognition();

onMounted(() => {
  speechRec.continuous = true;
  speechRec.interimResults = true;

  speechRec.onstart = () => {
    console.log("SpeechRec started");
    isRecording.value = true;
  };

  speechRec.onend = () => {
    console.log("Speech rec stopped");
    isRecording.value = false;
  };

  speechRec.onresult = (e) => {
    for (let i = 0; i < e.results.length; i++) {
      const result = e.results[i];

      if (result.isFinal) CheckForCommand(result);
    }

    const t = Array.from(e.results)
      .map((result) => result[0])
      .map((result) => result.transcript)
      .join("");

    transcript.value = t;
  };
});

const CheckForCommand = (result) => {
  const t = result[0].transcript;
  if (t.includes("stop" || "стоп")) {
    speechRec.stop();
  } else if (t.includes("what's the time") || t.includes("котра година")) {
    speechRec.stop();
    alert(new Date().toLocaleTimeString());
    setTimeout(() => speechRec.start(), 100);
  }
};

const ToggleMic = () => {
  if (isRecording.value) {
    speechRec.stop();
  } else {
    speechRec.start();
  }
};
</script>

<template>
  <div id="app">
    <button :class="`mic`" @click="ToggleMic">Record your speech</button>
    <div class="transcript" v-text="transcript"></div>
  </div>
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Fira sans", sans-serif;
}

body {
  background-color: #0d2824;
  color: #a2e3e7;
}

#app {
  background-color: rgb(66, 127, 145);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 600px;
  width: 100%;
}

button {
  width: 100%;
  border: none;
  outline: none;
  box-shadow: 4px 6px#213734;
  cursor: pointer;
  background-color: #71b1ab;
  color: #0e535d;
  font-size: 1.5rem;
  font-weight: 700;
  margin-bottom: 30px;
}

.transcript {
  color: #082e34;
  font-size: 2rem;
  font-weight: 500;
  font-style: italic;
  line-height: 1.5;
}
</style>
