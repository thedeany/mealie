<template>
  <div class="print">
    <PrintRecipe v-if="print" :recipe="recipeDetails" @exit="print = false" />
    <v-card v-else id="myRecipe">
      <v-img
        height="400"
        :src="getImage(recipeDetails.image)"
        class="d-print-none"
        :key="imageKey"
      >
        <v-btn
          absolute
          right
          color="accent lighten-1 "
          class="mt-2 mr-n2 image-action"
          @click="print = true"
        >
          <v-icon> mdi-printer </v-icon>
        </v-btn>
      </v-img>
      <ButtonRow
        :open="showIcons"
        @json="jsonEditor = true"
        @editor="
          jsonEditor = false;
          form = true;
        "
        @save="saveRecipe"
        @delete="deleteRecipe"
      />

      <ViewRecipe
        v-if="!form"
        :name="recipeDetails.name"
        :ingredients="recipeDetails.recipeIngredient"
        :description="recipeDetails.description"
        :instructions="recipeDetails.recipeInstructions"
        :tags="recipeDetails.tags"
        :categories="recipeDetails.categories"
        :notes="recipeDetails.notes"
        :rating="recipeDetails.rating"
        :yields="recipeDetails.recipeYield"
        :orgURL="recipeDetails.orgURL"
      />
      <VJsoneditor
        @error="logError()"
        class="mt-10"
        v-else-if="showJsonEditor"
        v-model="recipeDetails"
        height="1500px"
        :options="jsonEditorOptions"
      />
      <EditRecipe v-else v-model="recipeDetails" @upload="getImageFile" />
      <br />
    </v-card>
  </div>
</template>

<script>
import api from "../api";
import utils from "../utils";
import VJsoneditor from "v-jsoneditor";
import ViewRecipe from "./RecipeEditor/ViewRecipe";
import EditRecipe from "./RecipeEditor/EditRecipe";
import ButtonRow from "./UI/ButtonRow";
import PrintRecipe from "./RecipeEditor/PrintRecipe";

export default {
  components: {
    VJsoneditor,
    ViewRecipe,
    EditRecipe,
    ButtonRow,
    PrintRecipe,
  },
  data() {
    return {
      // CurrentRecipe: this.$route.params.recipe,
      print: false,
      form: false,
      jsonEditor: false,
      jsonEditorOptions: {
        mode: "code",
        search: false,
        mainMenuBar: false,
      },
      // Recipe Details //
      recipeDetails: {
        name: "",
        description: "",
        image: "",
        recipeYield: "",
        recipeIngredient: [],
        recipeInstructions: [],
        slug: "",
        filePath: "",
        url: "",
        tags: [],
        categories: [],
        dateAdded: "",
        notes: [],
        rating: 0,
      },
      imageKey: 1,
    };
  },
  mounted() {
    this.getRecipeDetails();
  },

  watch: {
    $route: function () {
      this.getRecipeDetails();
    },
  },

  computed: {
    CurrentRecipe() {
      return this.$route.params.recipe;
    },
    showIcons() {
      return this.form;
    },
    showJsonEditor() {
      if ((this.form === true) & (this.jsonEditor === true)) {
        return true;
      } else {
        return false;
      }
    },
  },
  methods: {
    getImageFile(fileObject) {
      this.fileObject = fileObject;
    },
    async getRecipeDetails() {
      this.recipeDetails = await api.recipes.requestDetails(this.CurrentRecipe);
      this.form = false;
    },
    getImage(image) {
      if (image) {
        return utils.getImageURL(image) + "?rnd=" + this.imageKey;
      }
    },
    deleteRecipe() {
      api.recipes.delete(this.recipeDetails.slug);
    },
    async saveRecipe() {
      console.log(this.recipeDetails);
      await api.recipes.update(this.recipeDetails);

      if (this.fileObject) {
        await api.recipes.updateImage(this.recipeDetails.slug, this.fileObject);
      }

      this.form = false;
      this.imageKey += 1;
    },
    showForm() {
      this.form = true;
      this.jsonEditor = false;
    },
  },
};
</script>

<style>
.card-btn {
  margin-top: -10px;
}
.disabled-card {
  opacity: 0.5;
}
.image-action {
  opacity: 0.9;
}
@media print {
  body {
    transform: scale(0.8);
  }
  table {
    page-break-inside: avoid;
  }
}
</style>