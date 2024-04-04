<template>
  <div>
    <v-btn
      icon
      round
      size="large"
      class="ma-3"
      elevation="3"
      @click="drawer = !drawer"
      color="light"
      style="
        position: fixed;
        left: 5px;
        top: 5px;
        margin: 0px !important;
        z-index: 999;
      "
      v-if="!drawer"
    >
      <v-icon>mdi-menu</v-icon>
    </v-btn>
    <!-- SIDEBAR -->
    <v-layout>
      <v-navigation-drawer v-model="drawer">
        <div style="position: relative">
          <div class="d-flex flex-column" style="height: 100%">
            <div
              class="d-flex px-3"
              style="
                align-items: center;
                justify-content: space-between;
                height: 60px;
              "
            >
              <div class="d-flex">
                <v-icon>mdi-robot-excited</v-icon>
              </div>

              <v-btn
                icon="mdi-close"
                size="medium"
                height="20"
                width="20"
                @click="drawer = !drawer"
                variant="text"
              ></v-btn>
            </div>

            <div class="flex-grow-1">
              <v-list>
                <v-list-item
                  @click="drawer = false"
                  link
                  to="/"
                  color="indigo-lighten-1"
                >
                  <div
                    class="d-flex justify-center align-center flex-column py-1"
                  >
                    <v-icon>mdi-home-roof</v-icon>
                    <v-list-item-title>Home</v-list-item-title>
                  </div>
                </v-list-item>

                <v-list-item
                  @click="drawer = false"
                  link
                  to="/chatgpt"
                  color="indigo-lighten-1"
                >
                  <div
                    class="d-flex justify-center align-center flex-column py-1"
                  >
                    <v-icon class="mx-2">mdi-robot-confused-outline</v-icon>
                    <v-list-item-title>Chatbot</v-list-item-title>
                  </div>
                </v-list-item>

                <v-list-item
                  @click="drawer = false"
                  link
                  to="/imageanalysis"
                  color="indigo-lighten-1"
                >
                  <div
                    class="d-flex justify-center align-center flex-column py-1"
                  >
                    <v-icon class="mx-2">mdi-image-filter-hdr</v-icon>
                    <v-list-item-title>Image Analysis</v-list-item-title>
                  </div>
                </v-list-item>
                <v-list-item
                  @click="drawer = false"
                  link
                  to="/imagegeneration"
                  color="indigo-lighten-1"
                >
                  <div
                    class="d-flex justify-center align-center flex-column py-1"
                  >
                    <v-icon class="mx-2">mdi-image-plus-outline</v-icon>
                    <v-list-item-title>Image Generation</v-list-item-title>
                  </div>
                </v-list-item>
              </v-list>
            </div>
          </div>
        </div>
      </v-navigation-drawer>
    </v-layout>
  </div>
</template>

<script setup>
import { ref } from "vue";
import { useRouter } from "#app";

const router = useRouter();

let drawer = ref(false);
</script>
