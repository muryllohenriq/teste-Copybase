<script lang="ts">
import axios from "axios";
import { IPokemon } from "../utils/interface";

export default {
  name: "App",

  components: {},

  data: () => {
    return {
      pokemons: [] as IPokemon[],
      search: "",
      toggleModal: false,
      selected_pokemon: [] as any,
    };
  },

  mounted() {
    axios
      .get("https://pokeapi.co/api/v2/pokemon?limit=151")
      .then((response) => {
        this.pokemons = response.data.results;
      });
  },

  methods: {
    get_id(pokemon: any) {
      return Number(pokemon.url.split("/")[6]);
    },
    get_name(pokemon: any) {
      return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
    },
    show_pokemon(id: number) {
      axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((response) => {
        this.selected_pokemon = response.data;
        this.toggleModal = !this.toggleModal;
      });
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
<template>
  <v-app>
    <v-container>
      <v-container>
        <input
          v-model="search"
          class="
            placeholder:italic placeholder:text-slate-400
            block
            bg-white
            w-full
            border border-slate-300
            rounded-md
            py-2
            pl-9
            pr-3
            shadow-sm
            focus:outline-none
            focus:border-sky-500
            focus:ring-sky-500
            focus:ring-1
            sm:text-sm
          "
          placeholder="Search for anything..."
          type="text"
          name="search"
        />
        <v-row>
          <v-col
            cols="2"
            v-for="pokemon in filtered_pokemons"
            :key="pokemon.name"
          >
            <v-card @click="show_pokemon(get_id(pokemon))">
              <v-container>
                <v-row class="mx-0 flex justify-center">
                  <img
                    :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(
                      pokemon
                    )}.png`"
                    :alt="pokemon.name"
                  />
                  <h2 class="text-center">{{ get_name(pokemon) }}</h2>
                </v-row>
              </v-container>
            </v-card>
          </v-col>
        </v-row>
      </v-container>
    </v-container>
    <div>
      <div
        class="
          fixed
          overflow-x-hidden overflow-y-auto
          inset-0
          flex
          justify-center
          items-center
        "
        v-if="toggleModal"
      >
        <div class="relative h-screen mx-auto w-auto max-w-2xl z-50">
          <div v-if="(selected_pokemon)" class="bg-white w-full rounded shadow-2xl flex flex-col">
            <div class="text-2xl font-bold">Header Text</div>
            <v-row>
              <v-col cols="4">
                <img
                  :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selected_pokemon.id}.png`"
                  :alt="selected_pokemon.name"
                />
              </v-col>
            </v-row>
            {{ selected_pokemon }}
            <button
              class="
                rounded
                bg-red-500
                text-white
                px-6
                mt-1
                py-2
                w-2/12
                m-auto
                mb-3
              "
              @click="toggleModal = false"
            >
              Close
            </button>
          </div>
        </div>
        <div
          v-if="toggleModal"
          class="absolute inset-0 z-40 opacity-25 bg-black"
        ></div>
      </div>
    </div>
  </v-app>
</template>

<style scoped>
</style>
