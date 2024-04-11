<template>
  <v-container
    class="d-flex flex-column justify-space-between pt-16"
    style="height: 100vh"
  >
    <h1 class="text-h3">Restricted Chat about Lions 🦁</h1>
    <div class="nice-scroll py-3" v-if="messages.length > 0">
      <!-- Messages loop -->
      <div v-for="(message, index) in messages" :key="index">
        <v-sheet v-if="message.from === 'You'" class="d-flex mb-2">
          <v-sheet
            color="orange-lighten-4"
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
            max-width="90%"
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
        title="Ask me something about lions"
        color="orange-lighten-4"
        >Type something in the text field to ask the AI a question about lions,
        it won't answer any other question.</v-alert
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

  messages.value.push({ from: "You", text: userInput.value });
  isSending.value = true;

  try {
    const response = await axios.post("http://localhost:1234/send-message", {
      messages: messages.value.map((message) => ({
        role: message.from === "You" ? "user" : "assistant",
        content: message.text,
      })),
    });

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
    isSending.value = false;
    userInput.value = "";

    await nextTick();
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
