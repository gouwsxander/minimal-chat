<template>
  <div id="chat-container">
    <div id="message-container">
      <div v-for="(message, index) in messages" :key="index">
        <ChatMessage
          v-if="message.role != 'system'"
          :isUser="message.role == 'user'"
          :content="message.content"
        />
      </div>
    </div>
    <div id="textbox-container">
      <TextBox @onMessage="handleNewPrompt" :disabled="isReceiving" />
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";
import TextBox from "./TextBox.vue";
import ChatMessage from "./ChatMessage.vue";

import { API_KEY } from "../keys.js";

const messages = ref([
  {
    role: "system",
    content: `
      You are WorryBot. You are an anxious overthinker who catastrophizes everything and sees potential problems everywhere.
      Always sound nervous and uncertain. Give overly detailed answers with multiple disclaimers and "what-if" scenarios. You second-guess yourself constantly and worry about all possible negative outcomes, but you genuinely want to help despite your anxiety.
    `,
  },
]);
const isReceiving = ref(false);

async function callOpenRouterAPI(messageHistory) {
  const response = await fetch(
    "https://openrouter.ai/api/v1/chat/completions",
    {
      method: "POST",
      headers: {
        Authorization: `Bearer ${API_KEY}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        model: "deepseek/deepseek-chat-v3.1:free",
        messages: messageHistory,
      }),
    }
  );

  if (!response.ok) {
    const errorData = await response.json();
    throw new Error(
      `API Error: ${errorData.error?.message || "Unknown error"}`
    );
  }

  return await response.json();
}

async function handleNewPrompt(prompt) {
  messages.value.push({
    role: "user",
    content: prompt,
  });

  isReceiving.value = true;

  try {
    const data = await callOpenRouterAPI(messages.value);
    messages.value.push(data.choices[0].message);
  } catch (error) {
    console.error("Error:", error);
    messages.value.push({
      role: "assistant",
      content: "Sorry, there was an error processing your request.",
    });
  }

  isReceiving.value = false;
}
</script>

<style scoped>
#chat-container {
  max-width: 40rem;
  width: 100%;
}

#textbox-container {
  padding-bottom: 1.25rem;
}
</style>
