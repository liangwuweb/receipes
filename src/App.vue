<template>
  <div id="app">
    <div class="container">
      <h1 class="mb-4">Recipes</h1>
      <div v-for="(recipe, index) in recipes" :key="index">
        <div class="recipe-box bg-success">
          <h3 class="text-left text-light p-3 mb-0 d-flex align-items-center justify-content-between" @click="toggleDropdown(recipe)">
            {{recipe.title}}
            <img style="width:30px" src="../src/assets/iconfinder_plus_172525.svg" alt="plus icon">
          </h3>
          <div v-if="recipe.dropdown" class="details text-left p-3 bg-light">
            <div class="image-wrap w-100 d-flex justify-content-center">
              <img class="img-fluid mb-4" :src="root() + responsiveImage(recipe.images)" alt="recipe image">
            </div>
            <span class="mr-3 mb-3"><span class="text-success font-weight-bold">Posted on</span> {{formatDate(recipe.postDate)}}</span>
            <span class="mr-3 mb-3"><span class="text-success font-weight-bold">Edited on</span> {{formatDate(recipe.editDate)}}</span>
            <br />
            <span class="mr-3 mb-3"><span class="font-weight-bold">Cook time:</span> {{recipe.cookTime}}</span>
            <span class="mr-3 mb-3"><span class="font-weight-bold">Prep time:</span> {{recipe.prepTime}}</span>
            <span><span class="font-weight-bold">Servings:</span> {{recipe.servings}}</span>
            <p><span class="font-weight-bold">Description:</span> {{recipe.description}}</p>
            <h4>Ingredients</h4>
            <ul v-for="ingredient in recipe.ingredients" :key="ingredient.uuid" class="font-weight-light">
              {{ingredient.amount}} {{ingredient.measurement}} {{ingredient.name}}.
              <ul v-if="ingredient.special != undefined">
                <li class="badge badge-warning">{{ingredient.special.title}}</li><br />
                <li class="badge badge-warning">{{ingredient.special.type}}</li><br />
                <li class="badge badge-warning text-left" style="white-space: normal;">{{ingredient.special.text}}</li>
              </ul>
            </ul>
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
      specials: [],
      array: [],
      dropDown: false
    };
  },
  mounted() {
    axios.get("http://localhost:3001/specials").then((res) => {
      this.specials = res.data;
      //console.log(res.data);
    });

    axios.get("http://localhost:3001/recipes").then((res) => {
      this.recipes = res.data.map((item) => {
        item.dropdown = false;
        return item;
      })
      //console.log(res.data);
      this.isSpecial();
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
    isSpecial: function () {
      this.recipes.forEach((recipe) => {
        recipe.ingredients.forEach((ingredient) => {
          //console.log(ingredient.name);
          this.specials.forEach((item) => {
            if (ingredient.uuid == item.ingredientId) {
              ingredient.special = item;
            }
          });
        });
      });
    },
    toggleDropdown: function(recipe){
      recipe.dropdown = !recipe.dropdown; 
    }
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

    h3 {
      cursor: pointer;
    }
  }
}
</style>
