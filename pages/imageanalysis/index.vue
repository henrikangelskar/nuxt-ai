<template>
  <v-container class="pt-16">
    <h1 class="text-h3 mb-5">Image Analysis</h1>

    <!-- Image Upload -->
    <v-row>
      <v-col>
        <input type="file" @change="previewImage" accept="image/*" />
      </v-col>
    </v-row>

    <!-- Display Uploaded Image -->
    <v-row v-if="imageUrl">
      <v-col>
        <v-sheet height="400">
          <v-img :src="imageUrl" cover aspect-ratio="1.7"></v-img>
        </v-sheet>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref } from "vue";

const imageUrl = ref("");

function previewImage(event) {
  const file = event.target.files[0];
  if (file) {
    const reader = new FileReader();
    reader.onload = (e) => {
      imageUrl.value = e.target.result;
    };
    reader.readAsDataURL(file);
  }
}
</script>
