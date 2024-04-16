<template>
  <v-sheet
    color="primary"
    class="d-flex justify-center text-center align-center pa-3"
    height="200"
  >
    <h1 class="text-h3">Smart Cognition AI Oversetter DEMO</h1>
  </v-sheet>
  <v-container class="pt-16">
    <v-row>
      <v-col cols="12" md="6">
        <h2 class="text-h4 mb-3">Sett inn din tekst</h2>
        <v-textarea
          v-model="inputText"
          label="Tekst"
          placeholder="Skriv tekst her..."
          rows="5"
          variant="outlined"
        ></v-textarea>
        <v-btn
          @click="processText"
          color="primary"
          class="mt-2 mb-10"
          block
          size="large"
          :loading="loading"
          :disabled="loading"
          >Transformer og Oversett</v-btn
        >
      </v-col>

      <div v-if="hasAnswered">
        <v-divider vertical></v-divider>
      </div>

      <v-col cols="12" md="6" v-if="hasAnswered">
        <v-sheet>
          <h4 class="text-h6 mb-2">Oversatt tekst til engelsk</h4>
          <v-textarea
            v-model="translatedText"
            label="Oversatt tekst"
            placeholder="Oversettelse vil komme her..."
            rows="5"
            variant="outlined"
          ></v-textarea>

          <h4 class="text-h6 mb-2">Forenklet tekst</h4>
          <v-textarea
            v-model="simplifiedText"
            label="Forenklet tekst"
            placeholder="Forenklet tekst vil komme her..."
            rows="5"
            variant="outlined"
          ></v-textarea>

          <h4 class="text-h6 mb-2">Relevante Snakkeark</h4>
          <v-select
            clearable
            chips
            label="Relevante snakkeark"
            variant="outlined"
            :items="allHeaders"
            v-model="selectedHeaders"
            multiple
            color="primary"
          ></v-select>

          <h4 class="text-h6 mb-2">Tekst til tale</h4>
          <v-btn
            @click="generateSpeech"
            :color="isSpeaking ? 'success' : 'primary'"
            class="mt-2"
            block
            size="large"
            :disabled="loadingSpeech || !inputText"
            :loading="loadingSpeech"
            variant="outlined"
            prepend-icon="mdi-account-voice"
          >
            {{
              isSpeaking
                ? "Snakker nå, trykk igjen for å slå av"
                : "Les Tekst Høyt"
            }}
          </v-btn>

          <h4 class="text-h6 mb-2 mt-6">Bildegenerering</h4>

          <v-text-field
            v-model="imagePrompt"
            label="Tekst til bildegenerering"
            placeholder="Prompt for bildegenerering vil vises her..."
            variant="outlined"
          ></v-text-field>

          <v-btn
            @click="generateImage"
            color="deep-purple accent-4"
            class="mt-2"
            block
            size="large"
            :disabled="loading"
            :loading="loading"
            variant="outlined"
            prepend-icon="mdi-image-area"
          >
            Generer bilde
          </v-btn>

          <v-sheet class="d-flex justify-center">
            <v-img
              class="mt-3 rounded-xl"
              :src="generatedImageUrl"
              max-height="500"
              max-width="500"
            ></v-img>
          </v-sheet>
        </v-sheet>
      </v-col>
    </v-row>
  </v-container>
</template>
<script setup>
import { ref } from "vue";
import axios from "axios";

const inputText = ref(
  "Invitasjon til Bowling med Oslo Fritidsklubb! Vi i Oslo Fritidsklubb har gleden av å invitere medlemmene våre til en morsom bowlingkveld! Arrangementet finner sted på Oslo Bowling Senter, lørdag den 15. oktober 2024, kl. 17:00. Dette er en flott mulighet til å møte nye venner, nyte litt vennlig konkurranse og ha det gøy. Vi sørger for snacks og forfriskninger. Vennligst RSVP innen den 10. oktober for å sikre din plass. Vi gleder oss til å se dere alle der for en strike-rik kveld!"
);
const translatedText = ref("");
const simplifiedText = ref("");
const allHeaders = ref([]);
const selectedHeaders = ref([]);
const loading = ref(false);
const hasAnswered = ref(false);
const isSpeaking = ref(false);
const audio = ref(null);
const loadingSpeech = ref(false);
const generatedImageUrl = ref(null);
const imagePrompt = ref("");

async function processText() {
  loading.value = true;

  const translatePromise = axios.post("http://127.0.0.1:1234/translate", {
    text: inputText.value,
  });
  const simplifyPromise = axios.post("http://127.0.0.1:1234/simplify", {
    text: inputText.value,
  });
  const headerPromise = axios.post("http://127.0.0.1:1234/snakkeark", {
    text: inputText.value,
  });
  const promptGenerationPromise = axios.post(
    "http://127.0.0.1:1234/generate-prompt",
    {
      text: inputText.value,
    }
  );

  try {
    const results = await Promise.all([
      translatePromise,
      simplifyPromise,
      headerPromise,
      promptGenerationPromise,
    ]);
    translatedText.value = results[0].data.translated_text;
    simplifiedText.value = results[1].data.simplified_text;
    allHeaders.value = results[2].data.all_headers;
    selectedHeaders.value = results[2].data.relevant_headers;
    imagePrompt.value = results[3].data.prompt;

    hasAnswered.value = true;
  } catch (error) {
    console.error("Error:", error);
    translatedText.value = "Sorry, there was an error processing your request.";
    simplifiedText.value = "Sorry, there was an error processing your request.";
  } finally {
    loading.value = false;
  }
}

async function generateSpeech() {
  if (isSpeaking.value && audio.value) {
    audio.value.pause();
    isSpeaking.value = false;
    return;
  }

  loadingSpeech.value = true; // Enable loading state
  try {
    const response = await axios.post(
      "http://127.0.0.1:1234/speech",
      {
        text: inputText.value,
      },
      {
        responseType: "blob",
      }
    );
    playAudio(response.data);
  } catch (error) {
    console.error("Error generating speech:", error);
  } finally {
    loadingSpeech.value = false; // Disable loading state after the request
  }
}

function playAudio(blob) {
  if (audio.value) {
    URL.revokeObjectURL(audio.value.src);
  }
  const url = URL.createObjectURL(blob);
  audio.value = new Audio(url);
  isSpeaking.value = true;
  audio.value.play();
  audio.value.onended = () => {
    isSpeaking.value = false;
    URL.revokeObjectURL(url);
  };
}

async function generateImage() {
  if (!imagePrompt.value.trim()) return;

  loading.value = true;
  try {
    const response = await axios.post("http://127.0.0.1:1234/generate-image", {
      prompt: imagePrompt.value,
    });
    if (response.data.image_url) {
      generatedImageUrl.value = response.data.image_url; // Set image URL for display
    } else {
      throw new Error("No image URL returned");
    }
  } catch (error) {
    console.error("Error generating image:", error);
  } finally {
    loading.value = false;
  }
}
</script>
