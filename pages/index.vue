<template>
  <v-container class="pt-16">
    <h1 class="text-h2 mb-5">Home</h1>

    <v-row>
      <v-col cols="12" sm="12" md="4">
        <v-card
          class="pa-3 rounded-xl"
          color="indigo-lighten-4"
          @click="goToChatGPT"
        >
          <v-icon size="96" class="ma-2">mdi-robot-outline</v-icon>
          <div class="d-flex align-center justify-space-between pa-3">
            <h3 class="text-h4">Chatbot</h3>
            <v-icon size="36">mdi-arrow-right</v-icon>
          </div>
        </v-card>
      </v-col>
      <v-col cols="12" sm="12" md="4">
        <v-card
          class="pa-3 rounded-xl"
          color="indigo-lighten-4"
          @click="goToImageAnalysis"
        >
          <v-icon size="96" class="ma-2">mdi-image-filter-hdr</v-icon>
          <div class="d-flex align-center justify-space-between pa-3">
            <h3 class="text-h4">Image analysis</h3>
            <v-icon size="36">mdi-arrow-right</v-icon>
          </div>
        </v-card>
      </v-col>
      <v-col cols="12" sm="12" md="4">
        <v-card
          class="pa-3 rounded-xl"
          color="indigo-lighten-4"
          @click="goToImageGen"
        >
          <v-icon size="96" class="ma-2">mdi-image-plus-outline</v-icon>
          <div class="d-flex align-center justify-space-between pa-3">
            <h3 class="text-h4">Img generation</h3>
            <v-icon size="36">mdi-arrow-right</v-icon>
          </div>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { useRouter } from "vue-router";
const router = useRouter();

function goToChatGPT() {
  router.push("/chatgpt");
}
function goToImageAnalysis() {
  router.push("/imageanalysis");
}
function goToImageGen() {
  router.push("/imagegeneration");
}
</script>
