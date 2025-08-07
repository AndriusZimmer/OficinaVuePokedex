<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

// 1. Crie uma variável reativa usando 'ref'
const pokemons = ref([])

// 2. Defina a função para buscar os dados da API
const fetchPokemons = async () => {
  try {
    const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=10')
    const pokemonData = response.data.results

    const detailedPokemons = await Promise.all(
      pokemonData.map(async (pokemon) => {
        const detailResponse = await axios.get(pokemon.url)
        return {
          name: pokemon.name,
          image: detailResponse.data.sprites.front_default,
        }
      }),
    )

    // 3. Atualize o valor da variável reativa usando '.value'
    pokemons.value = detailedPokemons
  } catch (error) {
    console.error('Erro ao buscar dados da API:', error)
  }
}

// 4. Chame a função quando o componente for montado
onMounted(fetchPokemons)
</script>

<template>
  <div class="pokemon-list">
    <h1>Lista de Pokémons</h1>
    <ul v-if="pokemons.length">
      <li v-for="pokemon in pokemons" :key="pokemon.name">
        <img :src="pokemon.image" :alt="pokemon.name" />
        {{ pokemon.name }}
      </li>
    </ul>
    <p v-else>Carregando Pokémons...</p>
  </div>
</template>

<style scoped>
.pokemon-list {
  text-align: center;
}

ul {
  list-style: none;
  padding: 0;
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 20px;
}

li {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  text-transform: capitalize;
  display: flex;
  flex-direction: column;
  align-items: center;
}

img {
  width: 96px;
  height: 96px;
}
</style>
