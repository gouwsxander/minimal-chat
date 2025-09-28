<template>
  <div :class="{ 'user-message': isUser, 'bot-message': !isUser }">
    {{ props.content }}
  </div>
</template>

<script setup>
import { defineProps } from "vue";

const props = defineProps({
  isUser: Boolean,
  content: String,
});
</script>

<style scoped>
div {
  padding: 1rem;
  margin: 1rem;
  color: black;
  font-size: 1rem;
  border-radius: 1rem;
}

.user-message {
  background-color: #f5f5f5;
}

.bot-message {
  background-color: white;
}
</style>
