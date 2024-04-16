<template>
  <v-container
    class="d-flex flex-column justify-space-between pt-16"
    style="height: 100vh"
  >
    <h1 class="text-h3">Image Generator with DALL·E 3</h1>
    <div class="nice-scroll" v-if="messages.length > 0">
      <!-- Image Display Loop -->
      <div
        v-for="(message, index) in messages"
        :key="index"
        class="d-flex justify-center mb-2"
      >
        <v-sheet v-if="message.isImage" color="transparent" class="py-2 px-3">
          <v-img
            :src="message.text"
            max-width="80%"
            aspect-ratio="1.7"
            class="my-4"
          ></v-img>
        </v-sheet>
      </div>
    </div>
    <v-sheet v-else>
      <v-alert
        icon="mdi-robot-outline"
        title="Let's generate something!"
        color="indigo-lighten-4"
      >
        Enter a prompt and generate an image.
      </v-alert>
    </v-sheet>

    <!-- Input Field for Image Prompt -->
    <form @submit.prevent="generateImage" class="pt-2">
      <v-text-field
        variant="outlined"
        v-model="userInput"
        label="Enter your prompt..."
        outlined
        dense
        single-line
        :disabled="isSending"
        :loading="isSending"
        append-inner-icon="mdi-send"
        @click:append-inner="generateImage"
      ></v-text-field>
    </form>
  </v-container>
</template>
<script setup>
import { ref } from "vue";
import axios from "axios";

const userInput = ref("A bird wearing a skirt");
const messages = ref([]);
const isSending = ref(false);

async function generateImage() {
  if (!userInput.value.trim()) return;

  isSending.value = true;
  try {
    const response = await axios.post(
      "https://api.openai.com/v1/images/generate",
      {
        model: "dall-e-3",
        prompt: userInput.value,
        size: "1024x1024",
        quality: "standard",
        n: 1,
      },
      {
        headers: {
          Authorization: `Bearer JHuW5oE2D372s2Ors1NhT3BlbkFJRzqbfRDgS4qy9ZrRkfyE`,
        },
      }
    );

    const imageUrl = response.data.data[0].url;

    messages.value.push({
      from: "Generated Image",
      text: imageUrl,
      isImage: true,
    });
  } catch (error) {
    console.error("Error:", error);
    // Handle error appropriately
  } finally {
    isSending.value = false;
    userInput.value = "";
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
