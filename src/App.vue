<template>
  <v-app>
    <v-container>
      <v-container>
        <v-row>
          <v-container>
            <v-img
              :src="require('../src/assets/pokedex.png')"
              class="my-3"
              contain
              height="200"
            />
          </v-container>
        </v-row>

        <v-text-field
          v-model="search"
          label="Buscar Pokemon"
          solo
        ></v-text-field>

        <v-row>
          <v-col
            cols="6"
            md="2"
            v-for="pokemon in filtered_pokemons"
            :key="pokemon.name"
          >
            <PokemonCard :pokemon="pokemon" @clicked="show_pokemon" />
          </v-col>
        </v-row>
      </v-container>
    </v-container>

    <PokemonInfoDialog
      :show.sync="show_dialog"
      :selected_pokemon="selected_pokemon"
    />
  </v-app>
</template>

<script>
import axios from "axios";

import PokemonCard from "./components/PokemonCard.vue";
import PokemonInfoDialog from "./components/PokemonInfoDialog.vue";

export default {
  name: "App",

  components: {
    PokemonCard,
    PokemonInfoDialog,
  },

  data() {
    return {
      pokemons: [],
      search: "",
      show_dialog: false,
      selected_pokemon: null,
    };
  },

  mounted() {
    axios
      .get("https://pokeapi.co/api/v2/pokemon?limit=897")
      .then((response) => {
        this.pokemons = response.data.results;
      });
  },
  methods: {
    show_pokemon(id) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selected_pokemon = response.data;
        this.show_dialog = !this.show_dialog;
      });
    },
    get_move_level(move) {
      for (let version of move.version_group_details) {
        if (
          version.version_group.name == "sword-shield" &&
          version.move_learn_method.name == "level-up"
        ) {
          return version.level_learned_at;
        }
      }
      return 0;
    },
  },
  computed: {
    filtered_pokemons() {
      return this.pokemons.filter((item) => {
        return item.name.includes(this.search);
      });
    },
  },
};
</script>

<style>
#app {
  /* background: linear-gradient(
      to bottom right,
      rgba(10, 10, 10, 1),
      rgba(12, 39, 63, 1)
    )
    no-repeat center center fixed !important;
  -webkit-background-size: cover;
  -moz-background-size: cover;
  -o-background-size: cover; */

  /* background-image: radial-gradient( circle farthest-corner at 10% 20%,  rgba(37,145,251,0.98) 0.1%, rgba(0,7,128,1) 99.8% ); */

  /* background-image: linear-gradient( 63.1deg,  rgba(5,23,111,1) 16.4%, rgba(24,95,240,1) 64.5% ); */

  background-image: radial-gradient(
    circle farthest-corner at 10% 20%,
    rgba(236, 27, 46, 1) 0%,
    rgba(147, 41, 30, 1) 90%
  );

  /* background-image: radial-gradient( circle 939px at 50.3% 51.7%,  rgba(50,166,255,1) 0%, rgba(24,98,235,1) 50.8%, rgba(50,166,255,1) 90% ); */

  /* background-image: radial-gradient( circle 822px at 14.3% 74.3%,  rgba(252,1,1,1) 0%, rgba(138,21,21,1) 66.2% ); */

  background-size: cover !important;
  background-position: center;
  min-height: 100vh;
}
</style>