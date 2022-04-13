<template>
  <BContainer>
    <h3 class="list-title">
      {{ listTitle }}
    </h3>
    <BRow class="justify-content-md-center">
      <template v-if="isExist">
        <BCol col lg="3" md="4" cols="12" sm="6" v-for="(movie, key) in list" :key="key">
          <MovieItem
            :movie="movie"
            @mouseover.native="onMouseOver(movie.Poster)"
            @removeItem="onRemoveItem"
            @showModal="onShowMovieInfo"
          />
        </BCol>
      </template>
      <template v-else>
        <div>Empty List</div>
      </template>
    </BRow>
    <BModal body-class="movie-modal-body" :id="movieInfoModalID" size="xl" hide-footer hide-header>
      <MovieInfoModalContent :movie="selectedMovie" @closeModal="onCloseModal"/> 
    </BModal>
  </BContainer>
</template>

<script>
import { mapActions, mapGetters } from "vuex";
import MovieItem from "./MovieItem";
import MovieInfoModalContent from "./MovieInfoModalContent";

export default {
  name: "MoviesList",
  components: {
    MovieItem,
    MovieInfoModalContent
  },
  data: () => ({
    movieInfoModalID: "movie-info",
    selectedMovieID: "",
  }),
  props: {
    list: {
      type: Object,
      default: () => ({}),
    },
  },
  computed: {
    ...mapGetters("movies", ["isSearch"]),
    isExist() {
      return Boolean(Object.keys(this.list).length);
    },
    listTitle() {
      return this.isSearch ? "Search result" : "TOP 250 FILMS";
    },
    selectedMovie() {
      return this.selectedMovieID ? this.list[this.selectedMovieID] : null;
    },
  },
  methods: {
    ...mapActions("movies", ["removeMovie"]),
    ...mapActions(["showNotify"]),
    onMouseOver(poster) {
      this.$emit("changePoster", poster);
    },
    async onRemoveItem(movie) {
      const isConfirmed = await this.$bvModal.msgBoxConfirm(
        `Are you sure delete ${movie.Title}?`
      );
      if (isConfirmed) {
        this.removeMovie(movie.imdbID);
        this.showNotify({
          msg: "Movie deleted successfull",
          variant: "success",
          title: "Success",
        });
      }
    },
    onShowMovieInfo(id) {
      console.log(id);
      this.selectedMovieID = id;
      this.$bvModal.show(this.movieInfoModalID);
    },
    onCloseModal() {
      this.selectedMovieID = null;
      this.$bvModal.hide(this.movieInfoModalID);
    }
  },
};
</script>

<style scoped>
.list-title {
  font-size: 50px;
  margin-bottom: 30px;
  color: #fff;
}
</style>

<style>
.movie-modal-body {
  padding: 0 !important;
}
</style>
