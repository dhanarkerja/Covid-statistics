<template>

<div v-if="$route.params.id == 0">
    <div v-if="loading">
        <h3>Sebentar</h3>
    </div>
    <div v-else>
        <h1>About DadJokes</h1>
        <label for="name">Recipe Name:</label>
        <input v-model=" inputRecipe.name " placeholder="Recipe name:" type="text" class="form-control" id="name">
        <br>
        <label for="ingredient">ingredient:</label>
        <textarea v-model=" inputRecipe.ingredient " placeholder="List ingredient :" type="text" class="form-control" id="ingredient"></textarea>
        <button type="button" class="btn btn-default" @click=" addCurrent ">Add</button>
    </div>
</div>
<div v-else>
    <div v-if="loading">
        <h3>Sebentar</h3>
    </div>
    <div v-else>
        <h1>About DadJokes</h1>
        <label for="name">Recipe Name:</label>
        <input v-model=" editRecipeName" placeholder="Recipe name:" type="text" class="form-control" id="name">
        <br>
        <label for="ingredient">ingredient:</label>
        <textarea v-model=" editIngredientList " placeholder="List ingredient :" type="text" class="form-control" id="ingredient"></textarea>
        <button type="button" class="btn btn-default"@click=" editCurrent ">Add</button>
    </div>
</div>
</template>
<script>
import axios from 'axios';
export default {
    data() {
        return {
            joke: [],
            inputRecipe: {
                name: '',
                ingredient:'',
            },
            editRecipeName:'',
            editIngredientList:'',
            loading:false
        }
    },
    async created() {
        const config = {
            headers: {
                Accept : "application/json"
            }
        };

        if(this.$route.params.id != 0)
        {
            this.loading = true
            try {
            const res = await axios.get(`http://excercise-api.outclass.co.id/api/g/recipes/${this.$route.params.id}`, config);

            this.editRecipeName = res.data.data.name;
            } catch (err) {
            console.log(err);
            }

            //Get Ingredient
            var tempEditIngredientList = []
            try {
            const res = await axios.get(`http://excercise-api.outclass.co.id/api/g/ingredients?filter[]=recipe_id,${this.$route.params.id}`, config);
            this.tempEditIngredientList = res.data.data;
            for (let i = 0; i < res.data.data.length; i++) {
                this.tempEditIngredientList[i] = res.data.data[i].name;
            }
            this.editIngredientList = this.tempEditIngredientList.join();
            console.log(this.editIngredientList)
            } catch (err) {
                console.log(err);
            } finally { 
                (this.loading = false);
            }
        }
    },
    methods: {
        async editCurrent(){
            const config = {
                headers: {
                    Accept : "application/json"
                }
            };
            this.loading = true
            //Get Ingredient
            var tempEditIngredientList = []
            try {
            const res = await axios.get(`http://excercise-api.outclass.co.id/api/g/ingredients?filter[]=recipe_id,${this.$route.params.id}`, config);
            for (let i = 0; i < res.data.data.length; i++) {
                this.tempEditIngredientList[i] = res.data.data[i];
                console.log(this.tempEditIngredientList[i])
            }
            } catch (err) {
                console.log(err);
            }
            console.log('wann')
            //edit recipe
            const res = await axios.patch('https://excercise-api.outclass.co.id/api/g/recipes/'+ this.$route.params.id, {
                "name" : this.editRecipeName
                }
            );
            console.log('wann')
            
            //json delete
            for (let i = 0; i < this.tempEditIngredientList.length; i++) {
                const res = await axios.delete("http://excercise-api.outclass.co.id/api/g/ingredients/" + this.tempEditIngredientList[i].id);
            }
            console.log('wann')
            //json insert
            var splitEditIngredientList = []
            this.splitEditIngredientList = this.editIngredientList.split(',');
            for (let i = 0; i < this.splitEditIngredientList.length; i++) {
                const res = await axios.post("http://excercise-api.outclass.co.id/api/g/ingredients",
                    {
                        "recipe_id" : this.$route.params.id,
                        "name": this.splitEditIngredientList[i]
                    }
                )
            }
            this.loading = false
            this.editIngredientList = this.splitEditIngredientList.join();
        },
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
            this.loading = true
            var findRecipeId=[];
            try {
                const res = await axios.get(`http://excercise-api.outclass.co.id/api/g/recipes`, config);
                
                this.findRecipeId = res.data.data;
                console.log(this.findRecipeId[this.findRecipeId.length-1].id)
                } catch (err) {
                    console.log(err);
                }   finally { 
                    (this.loading = false);
                }

            //input ingredients
            var tempInputIngredient=[]
             this.loading = true
            this.tempInputIngredient=this.inputRecipe.ingredient.split(',')
            for (let i =0; i <this.tempInputIngredient.length; i++) {
                const res = await axios.post("http://excercise-api.outclass.co.id/api/g/ingredients",
                {
                    "recipe_id" : this.findRecipeId[this.findRecipeId.length-1].id,
                    "name": this.tempInputIngredient[i]
                }
                )
               
            }
            
            this.loading = false
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
        }
    }
};
</script>
<style>

</style>