<template>
  <div class="class home">
    <Hero />
    <!-- Search -->

    <div class="container search">
      <input
        v-model.lazy="searchMovie"
        type="text"
        placeholder="Search"
        @keyup.enter="$fetch"
      />
      <button v-show="searchMovie !== ''" class="button" @click="clearSearch">
        Clear Search
      </button>
    </div>

    <!-- Loading -->
    <Loading v-if="$fetchState.pending" />

    <!-- Movies -->
    <div v-else class="container movies">
      <div v-if="searchMovie === ''" id="movie-grid" class="movies-grid">
        <Movie v-for="movie in movies" :key="movie.id" :movie="movie" @zzz="alert(`${movie.title}`)" />
      </div>

      <div v-else id="movie-grid" class="movies-grid">
        <Movie v-for="movie in searchedMovies" :key="movie.id" :movie="movie" @zzz="alert(`${movie.title}`)"/>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  data() {
    return {
      movies: [],
      searchedMovies: [],
      searchMovie: '',
    }
  },
  fetchDelay: 1500,
  async fetch() {
    if (this.searchMovie !== '') {
      await this.searchMovies()
      return
    }

    await this.getMovies()
  },
  head() {
    return {
      title: 'Movie App - Latest Streaming Movie Info',
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: 'Get all the latest stremaing movies in theaters & online',
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: 'movies, stream, streaming',
        },
      ],
    }
  },
  methods: {
    async getMovies() {
      const baseUrl = process.env.BASE_URL
      const apiKey = process.env.TMDB_API_KEY

      const url = `${baseUrl}movie/now_playing?api_key=${apiKey}&language=en-US&page=1`
      const data = await axios(url, { method: 'GET' })

      this.movies = data.data.results
    },
    async searchMovies() {
      const baseUrl = process.env.BASE_URL
      const apiKey = process.env.TMDB_API_KEY
      const url = `${baseUrl}search/movie?api_key=${apiKey}&language=en-US&page=1&query=${this.searchMovie}`

      const data = await axios(url, { method: 'GET' })
      this.searchedMovies = data.data.results
    },
    clearSearch() {
      this.searchMovie = ''
      this.searchedMovies = []
    },
    alert(title) {
      console.log(title);
      alert(title);
    }
  },
}
</script>

<style lang="scss">
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }
  .search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
  }
  .movies {
    padding: 32px 16px;
    .movies-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(3, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }
    }
  }
}
</style>
