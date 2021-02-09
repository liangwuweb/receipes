<template>
  <div id="app">
    <div class="container">
      <h1 class="mb-4">Recipes</h1>
      <div v-for="recipe in recipes" :key="recipe.uuid">
        <div class="recipe-box bg-success">
          <h3 class="text-left text-light p-3 mb-0">{{recipe.title}}</h3>
          <div class="details text-left p-3 bg-light">
            <div class="image-wrap w-100 d-flex justify-content-center">
              <img class="img-fluid mb-4" :src="root() + responsiveResize(recipe.images) === 'undefined' ? responsiveImage() : responsiveResize(recipe.images)" alt="recipe image">
            </div>
            <span class="mr-3 mb-3"><span class="text-success font-weight-bold">Posted on</span> {{formatDate(recipe.postDate)}}</span>
            <span class="mr-3 mb-3"><span class="text-success font-weight-bold">Edited on</span> {{formatDate(recipe.editDate)}}</span>
            <br />
            <span class="mr-3 mb-3"><span class="font-weight-bold">Cook time:</span> {{recipe.cookTime}}</span>
            <span class="mr-3 mb-3"><span class="font-weight-bold">Prep time:</span> {{recipe.prepTime}}</span>
            <span><span class="font-weight-bold">Servings:</span> {{recipe.servings}}</span>
            <p><span class="font-weight-bold">Description:</span> {{recipe.description}}</p>
            <h4>Ingredients</h4>
            <span v-for="ingredient in recipe.ingredients" :key="ingredient.uuid" class="font-weight-light">
              {{ingredient.amount}} {{ingredient.measurement}} {{ingredient.name}};
            </span>
            <h4>Instructions</h4>
            <span v-for="direction in recipe.directions" :key="recipe.directions.indexOf(direction)" class="font-weight-light">
              {{direction.instructions}}
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import moment from "moment";

export default {
  name: "App",
  components: {},
  data: function () {
    return {
      recipes: [],
    };
  },
  mounted() {
    axios.get("http://localhost:3001/recipes").then((res) => {
      this.recipes = res.data;
      console.log(res.data);
    });

    this.$nextTick(function () {
      window.addEventListener("resize", this.responsiveResize);

      //initialize
      this.responsiveResize();
    });
  },
  methods: {
    formatDate: function (date) {
      return moment(new Date(date)).format("MM-DD-YYYY, h:mm a");
    },
    root: function () {
      return "http://localhost:3001";
    },
    responsiveImage: function (images) {
      if (window.innerWidth >= 992) {
        return images.full;
      } else if (window.innerWidth >= 768 && window.innerWidth < 991) {
        return images.medium;
      } else {
        return images.small;
      }
    },
    responsiveResize: function () {
      if (window.innerWidth >= 992) {
        this.recipes.forEach((recipe) => {
          return recipe.images.full;
        });
      } else if (window.innerWidth >= 768 && window.innerWidth < 991) {
        this.recipes.forEach((recipe) => {
          return recipe.images.medium;
        });
      } else {
         this.recipes.forEach((recipe) => {
          return recipe.images.medium;
        });
      }
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;

  .recipe-box {
    border-bottom: 2px solid #2c3e50;
  }
}
</style>
