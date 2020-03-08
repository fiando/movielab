<template>
  <div class="col-lg-3 col-md-6 mb-4">
    <div class="card h-100">
      <img class="card-img-top" :src="movie.Poster" alt />
      <div class="card-body">
        <h4 class="card-title">{{movie.Title}}</h4>
        <h5>{{movie.Year}}</h5>
        <h6>{{movie.Type}}</h6>
        <p class="card-text">
        (<a :href="imbdDetail" target="_blank">{{movie.imdbID}}</a>)
        </p>
      </div>
      <div class="card-footer">
        <button type="button" class="btn btn-primary" @click="movieDetail">Find Out More!</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ReusableCard",
  props: ['movie'],
  computed: {
    imbdDetail: function() {
      return "https://www.imdb.com/title/" + this.movie.imdbID
    } 
  },
  methods: {
    movieDetail() {
      this.$emit('movieDetail', this.movie.imdbID);
    }
  },
};
</script>

<style>
</style>