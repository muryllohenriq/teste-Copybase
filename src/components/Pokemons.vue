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
      item: [] as any,
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
    filter_moves(pokemon: any) {
      return pokemon.moves.filter((item: any) => {
        let include = false;
        for(let version of item.version_group_details){
          if(version.version_group == 'sword-shield' && version.move_learn_method.name != 'machine'){
            include = true;
          }
        }
        return include
      })
    }
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
  <div>
    <div>
      <div>
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
        <div>
          <div
            cols="2"
            v-for="pokemon in filtered_pokemons"
            :key="pokemon.name"
          >
            <div @click="show_pokemon(get_id(pokemon))">
              <div>
                <div class="mx-0 flex justify-center">
                  <img
                    :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(
                      pokemon
                    )}.png`"
                    :alt="pokemon.name"
                  />
                  <h2 class="text-center">{{ get_name(pokemon) }}</h2>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
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
        <div class="relative mx-auto max-w-2xl z-50">
          <div
            v-if="selected_pokemon"
            class="bg-white w-64 rounded shadow-2xl flex flex-col h-52"
          >
            <div class="text-2xl font-bold">
              {{ get_name(selected_pokemon) }}
            </div>
            <div class="flex">
              <img
                :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${selected_pokemon.id}.png`"
                :alt="selected_pokemon.name"
              />
              <div
                label
                v-for="type in selected_pokemon.types"
                :key="type.slot"
                class="mr-1"
              >
                {{ type.type.name }}
              </div>
            </div>
            <span
              >Altura {{ selected_pokemon.height * 2.54 }} cm Peso
              {{ (selected_pokemon.weight * 0.45359237).toFixed(0) }} kgs</span
            >
            <button
              class="
                rounded
                bg-red-500
                text-white
                px-6
                mt-1
                py-2
                w-32
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
  </div>
</template>

<style scoped>
</style>
