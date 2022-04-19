<template>
    <div>
    <h1>About DadJokes</h1>
    <label for="name">Recipe Name:</label>
    <input v-model=" editRecipeName" placeholder="Recipe name:" type="text" class="form-control" id="name">
    <br>
    <label for="ingredient">ingredient:</label>
    <textarea v-model=" editIngredientList " placeholder="List ingredient :" type="text" class="form-control" id="ingredient"></textarea>
    <button type="button" class="btn btn-default" @click=" editCurrent ">Add</button>
    </div>
</template>

<script>
import axios from 'axios';
export default {
    data() {
        return {
            editRecipeName:'',
            editIngredientList:'',
        }
    },
    async created() {
        const config = {
            headers: {
                Accept : "application/json"
            }
        };
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
        }
    },
    methods: {
        async editCurrent(){
            const config = {
                headers: {
                    Accept : "application/json"
                }
            };
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
            this.editIngredientList = this.splitEditIngredientList.join();
        }
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