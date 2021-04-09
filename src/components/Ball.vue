<template>
  <main>
    <h1>{{ msg }}</h1>
    <p>Give browser the permission to use the microphone and say "up", "down",
    "left", "right" to control the movement of the ball</p>
    <div :class="animationclass"></div>
    <p>{{ diagnostic }}</p>
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

    let grammar = `#JSGF V1.0; grammar action; public <action> = up | down |
    right | left ;`
    let recognition = new (window as any).webkitSpeechRecognition();
    let speechRecognitionList = new (window as any).webkitSpeechGrammarList();

    const reset = () => {
      recognizing.value = false;
    }

    const toggleStartStop = () => {
      if(recognizing.value) {
        recognition.stop();
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
    recognition.onend = reset();

    recognition.onresult = function(event: SpeechRecognitionEvent) {
      for(let i = event.resultIndex; i < event.results.length; ++i) {
        if(event.results[i].isFinal) {
          console.log(event);
          let action = event.results[i][0].transcript;
          diagnostic.value = `moving ${action}`;
          animationclass.value = action;
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
  background-color: orangered;
}

button {
  margin-top: 1rem;
  color: rebeccapurple;
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
  transform: translateX(50px);
  transition: all 0.3s ease-in;
}

.left {
  transform: translateX(-50px);
  transition: all 0.3s ease-in;
}
</style>
