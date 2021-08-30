<template>
  <Loading v-if="$fetchState.pending" />
  <div v-else class="container single-movie">
    <NuxtLink :to="{ name: 'index' }" class="button">Back</NuxtLink>
    <div class="movie-info">
      <div class="movie-img">
        <img
          :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`"
          alt=""
        />
      </div>
      <div class="movie-content">
          <h1>{{movie.title}}</h1>
          <p class="movie-fact tagline">
              <span>Tagline:</span>
              "{{movie.tagline}}"
          </p>
          <p class="movie-fact">
              <span>Released:</span>
              {{new Date(movie.release_date).toLocaleString('en-US', {
                month: 'long',
                day: 'numeric',
                year: 'numeric'
              })}}
          </p>
          <p class="movie-fact">
              <span>Duration:</span>
              {{movie.runtime}} minutes
          </p>
          <p class="movie-fact">
              <span>Revenue:</span>
              {{movie.revenue.toLocaleString('en-US', {
                  style: 'currency',
                  currency: 'USD'
              })}}
          </p>
          <p class="movie-fact">
              <span>Overview:</span>
              {{movie.overview}}
          </p>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'SingleMovie',
  data() {
    return {
      movie: '',
    }
  },
  async fetch() {
    await this.getSingleMovie()
  },
  fetchDelay: 1000,
  head() {
    return {
      title: this.movie.title,
      meta: [
        {
          hid: 'description',
          name: 'description',
          content: 'Get all the latest stremaing movies in theaters & online'
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: 'movies, stream, streaming'
        }
      ]
    }
  },
  methods: {
    async getSingleMovie() {
      const baseUrl = process.env.BASE_URL
      const apiKey = process.env.TMDB_API_KEY

      const movieID = this.$route.params.movieid

      const url = `${baseUrl}movie/${movieID}?api_key=${apiKey}&language=en-US`
      const data = await axios(url, { method: 'GET' })

      this.movie = data.data
    },
  },
}
</script>

<style lang="scss" scoped>
.single-movie {
  color: #fff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 32px 16px;
  .button {
    align-self: flex-start;
    margin-bottom: 32px;
  }
  .movie-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 32px;
    color: #fff;
    @media (min-width: 800px) {
      flex-direction: row;
      align-items: flex-start;
    }
    .movie-img {
      img {
        max-height: 500px;
        width: 100%;
        @media (min-width: 800px) {
          max-height: 700px;
          width: initial;
        }
      }
    }
    .movie-content {
      h1 {
        font-size: 56px;
        font-weight: 400;
      }
      .movie-fact {
        margin-top: 12px;
        font-size: 20px;
        line-height: 1.5;
        span {
          font-weight: 600;
          margin-left: 2px;
          text-decoration: underline;
        }
      }
      .tagline {
        font-style: italic;
        span {
          font-style: normal;
        }
      }
    }
  }
}
</style>