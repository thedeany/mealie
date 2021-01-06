<template>
  <div>
    <v-card flat class="d-print-none">
      <v-card-text>
        <v-row align="center" justify="center">
          <v-btn
            left
            color="accent lighten-1 "
            class="ma-1 image-action"
            @click="$emit('exit')"
          >
            <v-icon> mdi-arrow-left </v-icon>
          </v-btn>
          <v-card flat class="text-center" align-center>
            <v-card-text>Font Size</v-card-text>
            <v-card-text>
              <v-btn
                class="mx-2"
                fab
                dark
                x-small
                color="primary"
                @click="subtractFontSize"
              >
                <v-icon dark> mdi-minus </v-icon>
              </v-btn>
              <v-btn
                class="mx-2"
                fab
                dark
                x-small
                color="primary"
                @click="addFontSize"
              >
                <v-icon dark> mdi-plus </v-icon>
              </v-btn>
            </v-card-text>
          </v-card>
        </v-row>
      </v-card-text>
    </v-card>
    <v-card flat ref="printView">
      <v-card-title>
        {{ recipe.name }}
      </v-card-title>
      <v-card-text>
        {{ recipe.description }}
      </v-card-text>
      <v-card-text>
        <v-row>
          <v-col cols="12" sm="12" md="4" lg="4">
            <h2>Ingredients</h2>

            <v-list dense class="ml-n4">
              <v-list-item
                v-for="(ingredient, index) in recipe.recipeIngredient"
                :key="generateKey('ingredient', index)"
                hide-details
                :label="ingredient"
              >
                <v-list-item-icon class="mr-1">
                  <v-icon> mdi-minus </v-icon>
                </v-list-item-icon>

                {{ ingredient }}
              </v-list-item>
            </v-list>
            <div v-if="recipe.categories[0]">
              <h2 class="mt-4">Categories</h2>
              <v-chip
                class="ma-1"
                color="primary"
                dark
                v-for="category in recipe.categories"
                :key="category"
              >
                {{ category }}
              </v-chip>
            </div>

            <div v-if="recipe.tags[0]">
              <h2 class="mt-4">Tags</h2>
              <v-chip
                class="ma-1"
                color="primary"
                dark
                v-for="tag in recipe.tags"
                :key="tag"
              >
                {{ tag }}
              </v-chip>
            </div>

            <h2 v-if="recipe.notes[0]" class="my-2">Notes</h2>
            <v-card
              flat
              class="mt-1"
              v-for="(note, index) in recipe.notes"
              :key="generateKey('note', index)"
            >
              <v-card-title> {{ note.title }}</v-card-title>
              <v-card-text>
                {{ note.text }}
              </v-card-text>
            </v-card>
          </v-col>
          <v-col cols="12" sm="12" md="8" lg="8">
            <h2 class="mb-4">Instructions</h2>

            <v-card
              v-for="(step, index) in recipe.recipeInstructions"
              :key="generateKey('step', index)"
              class="my-n4"
              flat
            >
              <v-card-title class="my-n4">Step: {{ index + 1 }}</v-card-title>
              <v-card-text class="my-n4">{{ step.text }}</v-card-text>
            </v-card>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>
  </div>
</template>

<script>
import utils from "../../utils";

export default {
  props: {
    recipe: Object,
  },
  data() {
    return {
      fontSize: 1.0,
    };
  },
  methods: {
    generateKey(item, index) {
      return utils.generateUniqueKey(item, index);
    },
    addFontSize() {
      this.fontSize += 0.2;
    },
    subtractFontSize() {
      this.fontSize -= 0.2;
    },
  },
};
</script>

<style>
</style>