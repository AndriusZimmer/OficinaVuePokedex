<script setup>
import { ref } from 'vue'
import axios from 'axios'

const pokemon = ref(null)

const pokemonNome = ref('')

const loading = ref(false)

const error = ref(null)

const buscarPokemon = async () => {
  if (!pokemonNome.value) return

  loading.value = true
  pokemon.value = null
  error.value = null

  try {
    const url = `https://pokeapi.co/api/v2/pokemon/${pokemonNome.value.toLowerCase()}`
    const response = await axios.get(url)

    pokemon.value = response.data
  } catch (err) {
    console.error('Erro ao buscar dados da API:', err)
    if (err.response && err.response.status === 404) {
      error.value = `Pokémon "${pokemonNome.value}" não encontrado.`
    } else {
      error.value = 'Ocorreu um erro ao buscar os dados.'
    }
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div>
    <header class="pokedex-header text-center">
      <h1>Pokedex</h1>
    </header>

    <form @submit.prevent="buscarPokemon">
      <div class="row py-2 justify-content-center align-items-center">
        <div class="col-12 col-md-6 col-lg-4">
          <input
            class="form-control form-control-sm"
            v-model="pokemonNome"
            required
            placeholder="Nome do Pokemon"
          />
        </div>
        <div class="col-12 col-md-auto mt-2 mt-md-0">
          <button class="btn btn-danger" type="submit">Procurar Pokemon</button>
        </div>
      </div>
    </form>

    <div v-if="loading" class="text-center mt-4">
      <div class="spinner-border text-primary" role="status">
        <span class="visually-hidden">Carregando...</span>
      </div>
      <p>Carregando...</p>
    </div>

    <div v-else-if="error" class="text-center mt-4 alert alert-danger" role="alert">
      {{ error }}
    </div>

    <div v-if="pokemon" class="container mt-4">
      <div class="card shadow-sm p-4">
        <div class="row">
          <div class="col-md-4 text-center">
            <img
              :src="pokemon.sprites.front_default"
              :alt="pokemon.name"
              class="img-fluid my-3 pokemon-sprite"
            />
            <h2 class="text-capitalize">{{ pokemon.name }}</h2>
          </div>

          <div class="col-md-8">
            <h4 class="mt-3">Tipos</h4>
            <div class="d-flex flex-wrap gap-2">
              <span v-for="type in pokemon.types" :key="type.slot" class="badge bg-secondary">
                {{ type.type.name }}
              </span>
            </div>

            <h4 class="mt-4">Habilidades</h4>
            <ul class="list-group">
              <li v-for="ability in pokemon.abilities" :key="ability.slot" class="list-group-item">
                {{ ability.ability.name }}
              </li>
            </ul>

            <h4 class="mt-4">Movimentos</h4>
            <ul class="list-group" style="height: 200px; overflow-y: auto">
              <li v-for="move in pokemon.moves" :key="move.move.name" class="list-group-item">
                {{ move.move.name }}
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.pokedex-header {
  background-color: #e53935;
  color: white;
  padding: 1.5rem 0;
  margin-bottom: 2rem;
}

.pokedex-header h1 {
  margin: 0;
}

.badge {
  text-transform: capitalize;
}

.list-group-item {
  text-transform: capitalize;
}

.pokemon-sprite {
  max-width: 350px;
  height: 350px;
  image-rendering: pixelated;
}
</style>
