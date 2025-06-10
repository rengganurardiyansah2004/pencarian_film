<template>
  <div id="app">
    <h1>Pencarian Film</h1>
    <form @submit.prevent="searchMovies">
      <input v-model="searchQuery" placeholder="Cari film..." />
      <button type="submit">Cari</button>
    </form>

    <!-- Conditional Rendering -->
    <p v-if="loading">Loading...</p>

    <!-- Declarative Rendering -->
    <div class="movies" v-else>
      <div v-for="movie in movies" :key="movie.imdbID" class="movie">
        <!-- Attribute Bindings -->
        <img :src="movie.Poster" :alt="movie.Title" />
        <h2>{{ movie.Title }}</h2>
      </div>
    </div>

    <!-- Conditional Rendering -->
    <p v-if="movies.length === 0 && !loading" class="no-movies-message">
      Tidak ada film yang ditemukan.
    </p>

    <!-- Event Listeners -->
    <button v-if="!loading && movies.length > 0" @click="loadMore">
      Muat Lebih Banyak
    </button>
    <p class="author-name">@Oleh Rengga Nur Ardiyansah</p>
  </div>
</template>

<script>
import './assets/style.css';

export default {
  data() {
    return {
      searchQuery: '',
      movies: [],
      loading: false,
      page: 1,
      indonesianMovies: [
        {
          Title: 'Laskar Pelangi',
          Poster:
            'http://4.bp.blogspot.com/-t_aEXPtU10U/Th0qs009d3I/AAAAAAAAALQ/VirlF07mOGU/s1600/laskar-pelangi2.jpg',
          imdbID: 'tt1197120',
        },
        {
          Title: 'Habibie dan Ainun',
          Poster:
            'http://4.bp.blogspot.com/-0uI2IjD5LJ4/UNtF9jPMvMI/AAAAAAAAC88/fDFciWDfDh4/s400/poster+film+ainun-habibie.png',
          imdbID: 'tt2368773',
        },
        {
          Title: 'Ayat-Ayat Cinta',
          Poster:
            'https://m.media-amazon.com/images/M/MV5BZmM2Y2Q3ZjktMmU5Ny00ZmIzLWJjYWEtYTIzZjQyZmM2NjZjXkEyXkFqcGdeQXVyNjUyMzY3MTM@._V1_SY1000_CR0,0,677,1000_AL_.jpg',
          imdbID: 'tt0969369',
        },
        {
          Title: 'Perahu Kertas',
          Poster: 'https://deelestari.com/wp-content/uploads/2014/09/perahukertas-jaketfilm.jpg',
          imdbID: 'tt2343744',
        },
        {
          Title: 'Agak laen',
          Poster:
            'https://tse4.mm.bing.net/th?id=OIP.U5BEUd1ed_M8g5i86ReuIAHaKl&pid=Api&P=0&h=220',
          imdbID: 'tt2519398',
        },
      ],
    };
  },
  methods: {
    async searchMovies() {
      this.movies = [];
      this.page = 1;
      this.loading = true;

      const searchTerm = this.searchQuery.toLowerCase();
      const foundMovie = this.indonesianMovies.find(movie =>
        movie.Title.toLowerCase().includes(searchTerm)
      );

      if (foundMovie) {
        this.movies = [foundMovie];
        this.loading = false;
        return;
      }
      try {
        const response = await fetch(
          `http://www.omdbapi.com/?s=${this.searchQuery}&apikey=YOUR_API_KEY&page=${this.page}`
        );
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        if (data.Search) {
          this.movies = data.Search.map(movie => ({
            Title: movie.Title,
            Poster: movie.Poster,
            imdbID: movie.imdbID,
          }));
        }
      } catch (error) {
        console.error('Error fetching movies:', error);
      } finally {
        this.loading = false;
      }
    },
    async loadMore() {
      this.page++;
      this.loading = true;
      try {
        const response = await fetch(
          `http://www.omdbapi.com/?s=${this.searchQuery}&apikey=YOUR_API_KEY&page=${this.page}`
        );
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        if (data.Search) {
          const newMovies = data.Search.map(movie => ({
            Title: movie.Title,
            Poster: movie.Poster,
            imdbID: movie.imdbID,
          }));
          this.movies = [...this.movies, ...newMovies];
        }
      } catch (error) {
        console.error('Error fetching more movies:', error);
      } finally {
        this.loading = false;
      }
    },
  },
};
</script>
