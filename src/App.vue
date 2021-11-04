<template>
  <h1>{{ title }}</h1>
  <PokemonList />
</template>

<script>
import PokemonList from './components/PokemonList.vue';


export default {
  name: 'App',
  components: {PokemonList},
  data() {
    return {
      title: "PokeList!!",
    }
  },
}
</script>

<style scoped>
  h1 {
    float: left;
    padding-left: 40px;
  }
</style>
