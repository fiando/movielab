<template>
  <div id="HomePage">
    <Navigation @reinitData="initData" />

    <div class="container-fluid">
      <header class="jumbotron my-4">
        <h1 class="display-5">Movielab! Discovery your favorite movie!</h1>
        <div class="row">
          <div class="col-md-6 offset-md-3">
            <form @submit="searchData">
              <div class="form-row">
                <div class="form-group col-md-12">
                  <input type="text" class="form-control" autofocus v-model.trim="searchTerm" />
                </div>
              </div>
              <div class="form-row">
                <div class="form-group col-md-6">
                  <select class="form-control" v-model="typeSelected">
                    <option value selected>All Type</option>
                    <option value="movie">Movie</option>
                    <option value="series">Series</option>
                    <option value="episode">Episode</option>
                  </select>
                </div>
                <div class="form-group col-md-6">
                  <select class="form-control" v-model="yearSelected">
                    <option value selected>All Year</option>
                    <option v-for="year in years" :key="year">{{ year }}</option>
                  </select>
                </div>
              </div>
              <button type="button" class="btn btn-primary" @click="searchData">Search</button>
            </form>
          </div>
        </div>
      </header>

      <div class="row text-center" id="ReusableCard">
        <div class="col-md-9">
          <div class="row">
            <ReusableCard
              v-for="(movie, index) in filteredMovies"
              :key="index"
              :movie="movie"
              @movieDetail="getDetail"
            />
          </div>
        </div>
        <div class="col-md-3">
          <div class="form-group">
            <input type="text" class="form-control" placeholder="Filter Name" v-model="filterName" />
          </div>
          <div class="card">
            <h3 class="card-header">Year</h3>
            <div class="card-body">
              {{fromYear}}
              <input
                type="range"
                id="vol"
                name="vol"
                :min="fromYear"
                :max="toYear"
                v-model="yearFilter"
              />
              {{toYear}}
              <br />
              <label for>From Year: {{yearFilter}}</label>
            </div>
          </div>
          <div class="card mt-3">
            <h3 class="card-header">Type</h3>
            <div class="card-body">
              <div class="form-check" v-for="(item, index) in filterTypes" :key="index">
                <input
                  class="form-check-input"
                  type="checkbox"
                  :value="item"
                  :id="item"
                  v-model="checkedTypes"
                  checked="checked"
                />
                <label class="form-check-label" :for="item">{{item}}</label>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <footer class="py-5 bg-dark">
      <div class="container">
        <p class="m-0 text-center text-white">Copyright &copy; Movielab 2020</p>
      </div>
    </footer>

    <MovieModal :movieDetail="movieDetail" :tableDetail="tableDetail" :ratingDetail="ratingDetail" />
  </div>
</template>

<script>
import ReusableCard from "./ReusableCard.vue";
import Navigation from "./Navigation.vue";
import MovieModal from "./MovieModal.vue";
import axios from "axios";
import keywords from "../data/keywords";

const apiEndpoint = "http://www.omdbapi.com/?apikey=7e3aba33";

export default {
  name: "HomePage",
  data() {
    return {
      movies: [],
      searchTerm: "",
      yearSelected: "",
      typeSelected: "",
      idSelected: "",
      tableDetail: [
        "Rated",
        "Released",
        "Runtime",
        "Genre",
        "Director",
        "Writer",
        "Actors",
        "Plot",
        "Language",
        "Country",
        "Awards",
        "Metascore",
        "imdbRating",
        "imdbVotes",
        "imdbID",
        "Type",
        "DVD",
        "BoxOffice",
        "Production",
        "Website"
      ],
      ratingDetail: [],
      movieDetail: {},
      randomList: keywords,
      fromYear: 0,
      toYear: 0,
      yearFilter: 0,
      filterName: "",
      checkedTypes: [],
      filterTypes: []
    };
  },
  watch: {
    movies: function(data) {
      if (!data.length) return;

      let yearList = [];

      for (const item of data) {
        if (this.filterTypes.indexOf(item.Type) == -1) {
          this.filterTypes.push(item.Type);
          this.checkedTypes.push(item.Type);
        }

        if (yearList.indexOf(item.Year) == -1) {
          let fromYear = item.Year.substr(0, 4);
          let toYear = item.Year.substr(5, 8);
          yearList.push(fromYear);

          for (let index = fromYear + 1; index < toYear; index++) {
            yearList.push(index);
          }
        }
      }

      this.toYear = Math.max(...yearList);
      this.fromYear = Math.min(...yearList);
      this.yearFilter = this.toYear;
    }
  },
  computed: {
    titleFilter: function() {
      return 0;
    },
    years: function() {
      let data = [];
      let year_to = new Date().getUTCFullYear();
      for (let index = year_to; index >= year_to - 100; index--) {
        data.push(index);
      }

      return data;
    },
    filteredMovies: function() {
      return this.movies.filter(item => {
        let filterYear =
          parseInt(item.Year.substr(0, 4)) <= this.yearFilter ||
          (item.Year.substr(5, 8).length &&
            item.Year.substr(5, 8) <= this.yearFilter);
        let filterType = this.checkedTypes.indexOf(item.Type) != -1;
        let filterName =
          item.Title.match(new RegExp(this.filterName, "gi")) !== null;

        if (filterYear && filterType && filterName) {
          return true;
        }
      });
    }
  },
  created() {
    this.initData();
  },
  methods: {
    getDetail(value) {
      axios
        .get(`${apiEndpoint}&i=${value}&plot=short`)
        .then(result => {
          this.movieDetail = result.data;
          this.ratingDetail = result.data.Ratings;

          window.$("#detail-modal").modal("show");
        })
        .catch(err => {
          console.log(err);
        });
    },
    getRandomItem() {
      return this.randomList[
        Math.floor(Math.random() * this.randomList.length)
      ];
    },
    initData() {
      this.searchTerm = "";
      this.yearSelected = "";
      this.typeSelected = "";
      axios
        .get(`${apiEndpoint}&s=${this.getRandomItem()}`)
        .then(result => {
          this.movies = result.data.Search;
        })
        .catch(err => {
          console.log(err);
        });
    },
    searchData(e) {
      e.preventDefault();

      let filter = `${apiEndpoint}&s=${this.searchTerm}`;

      if (this.yearSelected != "") {
        filter += "&y=" + this.yearSelected;
      }

      if (this.typeSelected != "") {
        filter += "&type=" + this.typeSelected;
      }

      axios
        .get(filter)
        .then(result => {
          if (result.data.Response === "False") {
            alert(result.data.Error);
            return;
          }

          this.movies = result.data.Search;
        })
        .catch(err => {
          console.log(err);
        });
    },
    filterMovie(data, e) {
      e.preventDefault();

      this.typeSelected = data;
      this.searchTerm = data;
      this.searchData();
    }
  },
  components: {
    ReusableCard,
    Navigation,
    MovieModal
  }
};
</script>

<style>
</style>