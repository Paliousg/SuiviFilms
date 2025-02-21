<template>
  <Page>
    <StackLayout>
      <Label text="Liste des films" class="title" />
      <ListView v-if="movies.length > 0" for="movie in movies" @itemTap="viewMovie">
        <v-template>
          <Label :text="movie.title" class="movie-item" />
        </v-template>
      </ListView>
      <Label v-else text="Aucun film disponible." class="empty-message" />
    </StackLayout>
  </Page>
</template>

<script>
import { api } from "~/api";
import { ApplicationSettings } from "@nativescript/core";
import MovieReview from "./MovieReview.vue";
import Login from "./Login.vue";

export default {
  data() {
    return {
      movies: [],
    };
  },
  async mounted() {
    try {
      const token = ApplicationSettings.getString("token");
      console.log("Token récupéré :", token);

      if (!token) {
        alert("Vous devez être connecté pour voir les films.");
        this.$navigateTo(Login);
        return;
      }

      console.log(" Récupération des films...");
      const response = await api.getMovies(token);

      if (!Array.isArray(response)) {
        console.error(" Réponse inattendue de l'API :", response);
        alert("Erreur lors de la récupération des films.");
        return;
      }

      this.movies = response;
      console.log("🎬 Films récupérés :", this.movies);
    } catch (error) {
      console.error(" Erreur lors de la récupération des films :", error);
      alert("Erreur lors de la récupération des films. Vérifiez votre connexion.");
    }
  },
  methods: {
    viewMovie(event) {
      const selectedMovie = this.movies[event.index];

      console.log("🎬 Film sélectionné :", selectedMovie);

      if (!selectedMovie) {
        alert("Erreur : Film introuvable.");
        return;
      }

      this.$navigateTo(MovieReview, {
        props: { selectedMovie },
      });
    },
  },
};
</script>
