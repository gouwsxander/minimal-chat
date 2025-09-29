<template>
  <div>
    <textarea
      v-model="prompt"
      @keydown.enter="handleKeydown"
      :style="{ height: getTextAreaHeight() + 'em' }"
      placeholder=""
    ></textarea>
    <button @click="sendPrompt" :disabled="props.disabled">Send</button>
  </div>
</template>

<script setup>
import { ref, defineEmits } from "vue";
import { defineProps } from "vue";

const emit = defineEmits(["onMessage"]);
const prompt = ref("");

const props = defineProps({
  disabled: Boolean,
});

function handleKeydown(event) {
  if (event.key === "Enter" && !event.shiftKey && !props.disabled) {
    event.preventDefault();
    sendPrompt();
  }
}

function sendPrompt() {
  emit("onMessage", prompt.value);
  prompt.value = "";
}

function getTextAreaHeight() {
  let lines = 1.15 * prompt.value.split("\n").length;
  return clamp(lines, 4, 16);
}

function clamp(value, min, max) {
  return Math.min(Math.max(value, min), max);
}
</script>

<style scoped>
textarea {
  font-size: 1rem;
  resize: none;
  width: 100%;
  /*height: auto;*/
  min-height: 4rem;
  margin-bottom: 0.5rem;
}

button {
  padding: 0.5rem 1rem;
  background-color: #0a8;
  color: white;
  border: none;
  border-radius: 0.25rem;
  cursor: pointer;
  font-size: 1rem;
}

button:hover {
  background-color: #086;
}

button:disabled {
  background-color: #777;
}
</style>
