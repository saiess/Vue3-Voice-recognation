<script setup lang="ts">
  import { ref, onMounted } from 'vue';
  const transcript = ref('');
  const isRecording = ref(false);

  const Recognition =
    window.SpeechRecognition || window.webkitSpeechRecognition;
  const speech = new Recognition();

  onMounted(() => {
    speech.continuous = true;
    speech.interimResults = true;

    speech.onstart = () => {
      console.log('speech started');
      isRecording.value = true;
    };

    speech.onend = () => {
      console.log('speech stopped');
      isRecording.value = false;
    };

    speech.onresult = (evt: any) => {
      for (let i = 0; i < evt.results.length; i++) {
        const result = evt.results[i];

        if (result.isFinal) checkForCommand(result);
      }
      const trp = Array.from(evt.results)
        .map((result: any) => result[0].transcript)
        .join('');

      transcript.value = trp;
    };
  });

  const checkForCommand = (result: any) => {
    const t = result[0].transcript;
    if (t.includes('stop recording')) {
      speech.stop();
    }
    else if (
      t.includes('what is the time') ||
      t.includes("what's the time")
    ) {
      speech.stop();
      alert(new Date().toLocaleTimeString());
      setTimeout(() => speech.start(), 100);
    }
  };
  const ToggleMic = () => {
    if (isRecording.value) {
      speech.stop();
    } else {
      speech.start();
    }
  };
</script>

<template>
  <div class="h-screen flex justify-center items-center bg-gradient-to-r from-indigo-700 via-green-800 to-cyan-600">
    <div class="flex justify-around items-center flex-col w-4/5 h-3/5">
      <h1 class="text-white text-3xl font-bold">Say somthing like (what is the time) & stop recording to stop</h1>
        <button :class="microphone" id="mic" @click="ToggleMic">Record</button>
      <div class="w-full flex justify-center">
        <div class="transcript w-4/5 p-8 text-gray-800 font-bold bg-slate-200 rounded-xl h-96" v-text="transcript">
          
        </div>
      </div>
    </div>
  </div>
</template>
