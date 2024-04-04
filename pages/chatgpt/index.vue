<template>
  <v-container
    class="d-flex flex-column justify-space-between pt-16"
    style="height: 100vh"
  >
    <h1 class="text-h3">Chatbot OpenAI 3.5 Turbo</h1>
    <div class="nice-scroll" v-if="messages.length > 0">
      <!-- Messages loop -->
      <div v-for="(message, index) in messages" :key="index">
        <v-sheet v-if="message.from === 'You'" class="d-flex mb-2">
          <v-sheet
            color="indigo-lighten-4"
            class="py-2 px-3 rounded-te-lg rounded-be-lg rounded-ts-lg"
            max-width="90%"
            min-width="100"
          >
            <p class="text-subtitle-2" style="font-size: 11px !important">
              From: You
            </p>
            <div class="d-flex align-center">
              <v-icon class="mr-2">mdi-account-outline</v-icon>
              <p style="line-height: 30px">{{ message.text }}</p>
            </div>
          </v-sheet>
        </v-sheet>
        <v-sheet v-else class="d-flex justify-end mb-2">
          <v-sheet
            color="blue-grey-lighten-4"
            class="py-2 px-3 rounded-te-lg rounded-bs-lg rounded-ts-lg"
            max-width="1000px"
            width="90%"
          >
            <p class="text-subtitle-2" style="font-size: 11px !important">
              From: ChatGPT 3.5
            </p>
            <div class="d-flex">
              <v-icon class="mr-2">mdi-robot-happy-outline</v-icon>
              <p style="line-height: 30px">{{ message.text }}</p>
            </div>
          </v-sheet>
        </v-sheet>
      </div>
    </div>

    <v-sheet v-else>
      <v-alert
        icon="mdi-robot-outline"
        title="Ask me something"
        color="indigo-lighten-4"
        >Type something in the text field to ask the AI a question.</v-alert
      >
    </v-sheet>

    <!-- Input field -->
    <form @submit.prevent="sendMessage" class="pt-2">
      <v-text-field
        variant="outlined"
        v-model="userInput"
        label="Type your message..."
        outlined
        dense
        single-line
        :disabled="isSending"
        :loading="isSending"
        append-inner-icon="mdi-send-variant-outline"
        @click:append-inner="sendMessage"
      ></v-text-field>
    </form>
  </v-container>
</template>

<script setup>
import { ref, nextTick } from "vue";
import axios from "axios";
import { useRoute } from "vue-router";

const userInput = ref("");
const messages = ref([]);
const isSending = ref(false);
const route = useRoute();

async function sendMessage() {
  if (!userInput.value.trim()) return;

  // Prepare the user message
  const userMessage = {
    role: "user",
    content: userInput.value,
  };

  // Push the user message to the conversation history
  messages.value.push({ from: "You", text: userInput.value });

  // Disable the input field while sending the message
  isSending.value = true;

  try {
    // Include the entire conversation history in the request
    const response = await axios.post(
      "https://api.openai.com/v1/chat/completions",
      {
        model: "gpt-3.5-turbo",
        messages: messages.value.map((message) => ({
          role: message.from === "You" ? "user" : "assistant",
          content: message.text,
        })),
      },
      {
        headers: {
          Authorization: `Bearer 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`,
        },
      }
    );

    // Push the ChatGPT response to the conversation history
    messages.value.push({
      from: "Chatbot",
      text: response.data.choices[0].message.content.trim(),
    });
  } catch (error) {
    console.error("Error:", error);
    messages.value.push({
      from: "Chatbot",
      text: "Sorry, there was an error processing your request.",
    });
  } finally {
    // Enable the input field
    isSending.value = false;
    userInput.value = "";

    // Scroll to the bottom to show the latest message
    await nextTick();
    const container = document.querySelector(".messages-container");
    container.scrollTop = container.scrollHeight;
  }
}
</script>

<style scoped>
.nice-scroll {
  overflow-y: auto;
  height: 100%;
}

/* Chrome, Edge, and Safari */
.nice-scroll::-webkit-scrollbar {
  width: 8px; /* scrollbar width */
  background-color: transparent; /* scrollbar background */
}

.nice-scroll::-webkit-scrollbar-thumb {
  background-color: #aaaaaa; /* scrollbar thumb color */
  border-radius: 16px; /* scrollbar thumb corner rounding */
}

/* Firefox */
.nice-scroll {
  scrollbar-width: thin; /* "auto" or "thin" */
  scrollbar-color: #00000099 transparent; /* thumb and track color */
}

.messages-container {
  display: flex;
  flex-direction: column;
  justify-content: end;
  height: 100%;
  border: 2px solid red;
  overflow: scroll;
}
</style>
