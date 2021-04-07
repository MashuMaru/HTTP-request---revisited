<template>
  <section>
    <base-card>
      <h2>Submitted Experiences</h2>
      <div>
        <base-button v-on:click="loadExperiences">Load Submitted Experiences</base-button>
    </div>
    <p v-if="error">Error in retrieving data.</p>
    <p v-if="isLoading">Data is loading.</p>
    <p v-else-if="checkDataExists()">No stored experiences found. Add some results first.</p>
      <ul v-else-if="!isLoading && results.length > 0">
        <SurveyResult
          v-for="result in results"
          :key="result.id"
          :name="result.name"
          :rating="result.rating"
        />
      </ul>
    </base-card>
  </section>
</template>

<script>
import SurveyResult from './SurveyResult.vue';

export default {
  // props: ['results'],
  components: {
    SurveyResult
  },
  data: function() {
    return {
      results: [],
      isLoading: false,
      error: false,
    };
  },
  methods: {
    checkDataExists() {
      return !this.isLoading && (!this.results || this.results.length === 0);
    },
    loadExperiences() {
      this.isLoading = true;
      fetch('https://vue-http-demo-d8f01-default-rtdb.firebaseio.com/surveys.json', {
        method: 'GET',
      }).then((response) => {
        if(response.ok) {
          return response.json();
        }
      }).then((data) => {
        this.isLoading = false;
        const resultList = [];
        for (const id in data) {
          resultList.push({
            id: data[id],
            name: data[id].name,
            rating: data[id].rating,
          })
          this.results = resultList;
        }
      }).catch((error) => {
        console.log(error);
        this.error = true;
      })
    }
  },
  mounted: function() {
    this.loadExperiences();
  },
};
</script>

<style scoped>
ul {
  list-style: none;
  margin: 0;
  padding: 0;
}
</style>
