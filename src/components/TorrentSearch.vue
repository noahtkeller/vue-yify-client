<template>
  <v-toolbar color="#FFF" elevation="2">
    <v-img :src="yifyLogo"></v-img>
    <v-toolbar-title>YIFY Torrents API</v-toolbar-title>
    <v-btn class="recent-btn" outlined elevation="1">Recently Added</v-btn>
    <v-text-field clearable required prepend-inner-icon="mdi-magnify" outlined label="Search Movies" v-model="termsLocal"></v-text-field>
  </v-toolbar>
</template>

<script>
import yifyLogo from '../assets/img/logo-YTS.svg';

export default {
  name: 'TorrentSearch',
  setup() {
    return { yifyLogo };
  },
  methods: {
    async onSubmit() {
      this.$emit('search', this.termsLocal);
    },
    searchComplete() {
      this.enabled = true;
    },
  },
  data() {
    return { termsLocal: this.terms };
  },
  emits: ['search'],
  props: ['terms'],
  watch: {
    terms(terms) {
      this.termsLocal = terms || '';
    }
  }
}
</script>

<style lang="sass" scoped>
.recent-btn
  margin-right: 0.5rem
</style>
