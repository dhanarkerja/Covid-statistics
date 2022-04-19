<template>
  <div>
    <h1>About DadJokes</h1>
    <label for="name">Recipe Name:</label>
    <input v-model=" inputRecipe.name " placeholder="Recipe name:" type="text" class="form-control" id="name">
    <br>
    <label for="ingredient">ingredient:</label>
    <textarea v-model=" inputRecipe.ingredient " placeholder="List ingredient :" type="text" class="form-control" id="ingredient"></textarea>
    <button type="button" class="btn btn-default" @click=" addCurrent ">Add</button>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      inputRecipe: {
        name: '',
        ingredient:'',
      },
      //findRecipeId:[]
    };
  },
  async created() {
    const config = {
      headers: {
        Accept: "application/json"
      }
    };

    try {
      const res = await axios.get("http://excercise-api.outclass.co.id/api/g/recipes", config);

      this.jokes = res.data.data;
    } catch (err) {
      console.log(err);
    }
  },
  methods: {
    async addCurrent() {
      const config = {
        headers: {
          Accept: "application/json"
        }
      };
      //input recipe
      try {
        const res = await axios.post(
          `http://excercise-api.outclass.co.id/api/g/recipes`,
          {
            "name": this.inputRecipe.name
          },
          config);
      } catch (err) {
        console.log(err);
      }

      //get array for id recipe
      var findRecipeId=[];
      try {
        const res = await axios.get(`http://excercise-api.outclass.co.id/api/g/recipes`, config);

        this.findRecipeId = res.data.data;
        console.log(this.findRecipeId[this.findRecipeId.length-1].id)
        } catch (err) {
            console.log(err);
        }

      //input ingredients
      var tempInputIngredient=[]
      this.tempInputIngredient=this.inputRecipe.ingredient.split(',')
      for (let i =0; i <this.tempInputIngredient.length; i++) {
        const res = await axios.post("http://excercise-api.outclass.co.id/api/g/ingredients",
          {
            "recipe_id" : this.findRecipeId[this.findRecipeId.length-1].id,
            "name": this.tempInputIngredient[i]
          }
        )
      }
      //cleaning input keyword
      this.inputRecipe.name='';
      this.inputRecipe.ingredient='';
    },
  },
  head() {
    return {
      title: "Dad Jokes",
      meta: [
        {
          hid: "description",
          name: "description",
          content: "Best place for corny dad jokes"
        }
      ]
    };
  }
};
</script>

<style>
</style>