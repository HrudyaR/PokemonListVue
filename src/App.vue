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
      title: "Pokemon!!",
    }
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
