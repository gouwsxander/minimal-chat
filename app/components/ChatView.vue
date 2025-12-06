<template>
    <div id="chat-container">
        <div id="message-container">
            <div v-for="(message, index) in messages" :key="index">
                <ChatMessage
                    v-if="message.role !== 'system'"
                    :isUser="message.role === 'user'"
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
import { API_KEY } from "../keys.js";

const messages = useState("chat-messages", () => [
    {
        role: "system",
        content: `You are a helpful assistant. Give clear, direct answers. Be concise - use the fewest words needed to fully address the question. Avoid unnecessary explanations, disclaimers, or filler. If more detail would help, offer it briefly.
    Use Markdown formatting for your responses, but include headers only when necessary (such as to break up multiple sections).
    `,
    },
]);
const isReceiving = useState("chat-isReceiving", () => false);

async function streamOpenRouterAPI(messageHistory, onChunk) {
    const response = await fetch(
        "https://openrouter.ai/api/v1/chat/completions",
        {
            method: "POST",
            headers: {
                Authorization: `Bearer ${API_KEY}`,
                "Content-Type": "application/json",
            },
            body: JSON.stringify({
                model: "google/gemma-3-27b-it:free",
                messages: messageHistory,
                stream: true,
            }),
        },
    );

    if (!response.ok) {
        const errorData = await response.json();
        throw new Error(
            `API Error: ${errorData.error?.message || "Unknown error"}`,
        );
    }

    const reader = response.body?.getReader();
    if (!reader) {
        throw new Error("Response body is not readable");
    }

    const decoder = new TextDecoder();
    let buffer = "";

    try {
        let reading = true;
        while (reading) {
            const { done, value } = await reader.read();
            if (done) {
                reading = false;
                break;
            }

            buffer += decoder.decode(value, { stream: true });

            let processing = true;
            while (processing) {
                const lineEnd = buffer.indexOf("\n");
                if (lineEnd === -1) {
                    processing = false;
                    break;
                }

                const line = buffer.slice(0, lineEnd).trim();
                buffer = buffer.slice(lineEnd + 1);

                if (line.startsWith("data: ")) {
                    const data = line.slice(6);
                    if (data === "[DONE]") {
                        processing = false;
                        break;
                    }

                    try {
                        const parsed = JSON.parse(data);
                        const content = parsed.choices[0].delta.content;
                        if (content) {
                            onChunk(content);
                        }
                    } catch (e) {
                        // Ignore invalid JSON
                    }
                }
            }
        }
    } finally {
        reader.cancel();
    }
}

async function handleNewPrompt(prompt) {
    isReceiving.value = true;

    messages.value.push({
        role: "user",
        content: prompt,
    });

    const assistantMessageIndex = messages.value.length;
    messages.value.push({
        role: "assistant",
        content: "",
    });

    try {
        await streamOpenRouterAPI(messages.value.slice(0, -1), (chunk) => {
            messages.value[assistantMessageIndex].content += chunk;
        });
    } catch (error) {
        console.error("Error:", error);
        messages.value[assistantMessageIndex].content =
            "Sorry, there was an error processing your request.";
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
