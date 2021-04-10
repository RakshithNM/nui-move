<template>
  <main>
    <h1>{{ msg }}</h1>
    <p><strong>Click the start button, give browser the permission to use the microphone and say "UP" or "DOWN" or
    "LEFT" or "RIGHT" to control the movement of the ball</strong></p>
    <div :class="animationclass"></div>
    <p class="diagnostic"><strong>{{ diagnostic }}</strong></p>
    <button @click="toggleStartStop">{{ recognizing ? "STOP" : "START" }}</button>
  </main>
</template>

<script lang="ts">
import { defineComponent, onMounted, ref } from 'vue'

export default defineComponent({
  name: 'Ball',
  props: {
    msg: {
      type: String,
      required: true
    }
  },
  setup: () => {
    const animationclass = ref("")
    const diagnostic = ref("")
    const recognizing = ref(false)

    let grammar = `#JSGF V1.0; grammar action; public <action> = up | down | right | left ;`
    let recognition = new (window as any).webkitSpeechRecognition();
    let speechRecognitionList = new (window as any).webkitSpeechGrammarList();

    const reset = () => {
      animationclass.value = "center";
      diagnostic.value = "";
      recognizing.value = false;
    }

    const toggleStartStop = () => {
      if(recognizing.value) {
        recognition.abort();
        console.log("stopped recognition")
        reset();
      }
      else {
        recognition.start();
        console.log("started recognition")
        recognizing.value = true;
      }
    }

    speechRecognitionList.addFromString(grammar, 1);
    recognition.grammars = speechRecognitionList;
    recognition.continuous = true;
    recognition.lang = 'en-US';
    recognition.interimResults = true;
    recognition.maxAlternatives = 1;
    reset();
    recognition.onend = recognition.start;

    recognition.onresult = function(event: SpeechRecognitionEvent) {
      for(let i = event.resultIndex; i < event.results.length; ++i) {
        if(event.results[i].isFinal) {
          console.log(event);
          let action = event.results[i][0].transcript.trim();
          if(action.split(" ").length > 1) {
            action = action.split(" ")[0];
          }
          let possibleMovements = ['up', 'down', 'right', 'left']
          if(possibleMovements.includes(action.trim().toLowerCase())) {
            diagnostic.value = `moving ${action}`;
            animationclass.value = action;
          } else {
            diagnostic.value = `you said something else or browser has no confidence(this can happen if you use a bluetooth mic) that you said the one of the right words(up, down, right, left)`
          }
        }
      }
    }

    return { 
      animationclass,
      diagnostic,
      recognizing,
      toggleStartStop
    }
  }
})
</script>

<style scoped>
main {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

div {
  margin-top: 1rem;
  border-radius: 50%;
  width: 50vmin;
  aspect-ratio: 9/9;
  display: flex;
  justify-content: center;
  align-items: center;
  background: linear-gradient(#f00202 0%, #bd0404 10%, #7d0404 50%, #4a0202 100%);
  position: relative;
}

div::before{
  content: "";
  width: 40vmin;
  aspect-ratio: 9/9;
  background: linear-gradient(#f06060 0%, #a30505 60%, #4d0202 100%);

  border-radius: 50%;
  filter: blur(18px);
}

div::after{
  content: '';
  position: absolute;
  width: 100%;
  height: 60px;
  border-radius: 50%;
  background: rgba(182, 179, 179, 0.6);
  bottom: -60px;
  z-index: -1;
  filter: blur(10px);
}

button {
  cursor: pointer;
  margin-top: 2rem;
  color: rebeccapurple;
}

.diagnostic {
  margin-top: 8rem;
}

.up {
  transform: translateY(-50px);
  transition: all 0.3s ease-in;
}

.down {
  transform: translateY(50px);
  transition: all 0.3s ease-in;
}

.right {
  transform: translateX(150px);
  transition: all 0.3s ease-in;
}

.left {
  transform: translateX(-150px);
  transition: all 0.3s ease-in;
}

.center {
  transform: translate(0px);
  transition: all 0.3s ease-in;
}
</style>
