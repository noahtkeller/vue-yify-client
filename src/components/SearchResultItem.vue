<template>
  <v-hover v-slot="{ isHovering, props }">
    <v-card :elevation="isHovering ? 8 : 2" @click="movieDetails(movie.id)" outlined v-bind="props">
      <v-card-title>
        <span>{{ movie.title_english }}</span>
      </v-card-title>
      <v-img cover :src="movie.large_cover_image" aspect-ratio=".66">
        <template #sources>
          <source :srcset="movie.medium_cover_image">
          <source :srcset="movie.small_cover_image">
        </template>
        <template v-slot:placeholder>
          <v-row class="fill-height ma-0" align="center" justify="center">
            <v-progress-circular indeterminate color="grey lighten-5"></v-progress-circular>
          </v-row>
        </template>
        <v-fade-transition>
          <div v-if="isHovering" class="btn-container">
            <h1 class="rating"><v-icon class="star-icon">mdi-star</v-icon></h1>
            <h2 class="mdc-theme--on-primary rating">{{ movie.rating }} / 10</h2>
            <div><v-btn @click.stop="watchMovie(movie.id)" class="mdc-theme--secondary-bg mdc-theme--on-secondary">Watch&nbsp;<v-icon>mdi-play</v-icon></v-btn></div>
            <h2 class="mdc-theme--on-primary rating">{{ movie.genres.slice(0, 2).join(' ') }}</h2>
          </div>
        </v-fade-transition></v-img>
    </v-card>
  </v-hover>
</template>

<script>
export default {
  name: 'SearchResultItem',
  props: ['movie'],
  emits: ['watchMovie', 'movieDetails'],
  methods: {
    watchMovie(id) {
      this.$emit('watchMovie', id);
    },
    movieDetails(id) {
      this.$emit('movieDetails', id);
    }
  },
  data() {
    return { overlay: false };
  },
};
</script>

<style lang="sass">
@use "@material/theme"
@use "@material/elevation"

.btn-container
  display: flex
  flex-direction: column
  justify-content: center
  height: 100%
  row-gap: 1rem
  background-color: rgba(102, 102, 102, .75)

.rating
  user-select: none
  cursor: pointer
  .star-icon
    font-size: 5rem
    color: theme.$amber-200

.v-card
  text-align: center
  cursor: pointer
  @include elevation.elevation(1)
  .v-card-title
    white-space: nowrap
    span
      overflow: hidden
      text-overflow: ellipsis
</style>
