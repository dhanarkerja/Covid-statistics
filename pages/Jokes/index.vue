<template>
<div v-if="loading">
  <h3>Sebentar iya</h3>
</div>
<div v-else>
  <Joke v-for="joke in jokes" :key="joke.id" :name="joke.Country"/>
  </div>
</template>

<script>
import axios from "axios";
import Joke from "../../components/Jokes.vue";
import SearchJokes from "../../components/SearchJokes.vue";

export default {
  components: {
    Joke
  },
  data() {
    return {
      jokes: [],
      loading: false
    };
  },
  async created() {
    const config = {
      headers: {
        Accept: "application/json"
      }
    };
    this.loading = true
    try {
      const res = await axios.get("https://api.covid19api.com/summary", config);
      this.jokes=res.data.Countries
      console.log(this.jokes)
    } catch (err) {
      console.log(err);
    } finally {
      (this.loading = false);
    }
  },
  methods: {
    async searchText(text) {
      const config = {
        headers: {
          Accept: "application/json"
        }
      };

      try {
        const res = await axios.get(
          `https://icanhazdadjoke.com/search?term=${text}`,
          config
        );

        this.jokes = res.data.results;
      } catch (err) {
        console.log(err);
      }
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
    };
  }
};
</script>

<style>
</style>