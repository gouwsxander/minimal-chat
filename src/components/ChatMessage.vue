<template>
  <div
    id="container"
    :class="{ 'user-message': isUser, 'bot-message': !isUser }"
  >
    <!-- {{ props.content }} -->
    <div id="messageContent" v-html="covertToHtml(props.content)"></div>
  </div>
</template>

<script setup>
import { defineProps } from "vue";
import showdown from "showdown";

const props = defineProps({
  isUser: Boolean,
  content: String,
});

var converter = new showdown.Converter();
function covertToHtml(text) {
  return converter.makeHtml(text);
}
</script>

<style scoped>
#container {
  padding: 0.1rem 1.8rem;
  margin: 1rem;
  color: black;
  font-size: 1rem;
  border-radius: 1rem;
}

#messageContent {
  padding: 0rem;
  margin: 0rem;
}

.user-message {
  background-color: #f5f5f5;
}

.bot-message {
  background-color: white;
}
</style>
