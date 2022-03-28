<template>
  <div class="search-results-container">
    <SearchResultItem @movieDetails="movieDetails" @watchMovie="watchMovie" v-for="(movie, idx) of movies" v-bind:key="idx" :movie="movie" />
  </div>
</template>

<script>
import SearchResultItem from './SearchResultItem';

export default {
  name: 'SearchResults',
  props: ['terms'],
  components: {
    SearchResultItem,
  },
  data() {
    return { movies: [] };
  },
  methods: {
    async getResults() {
      this.movies = [];
      const res = await fetch(`https://yts.mx/api/v2/list_movies.json?query_term=${this.terms}`);
      const { data: { movies = [] } = {} } = await res.json() || {};
      if (movies) {
        this.movies.push(...movies);
      }
    },
    watchMovie(id) {
      this.$emit('watchMovie', id);
    },
    movieDetails(id) {
      this.$emit('movieDetails', id);
    },
  },
  watch: {
    async terms() {
      await this.getResults();
    }
  },
  async mounted() {
    await this.getResults();
  }
}
</script>

<style scoped lang="sass">
.search-results-container
  display: grid
  gap: 2rem
  grid-template-columns: 1fr 1fr 1fr 1fr
  padding-bottom: 2rem
</style>
