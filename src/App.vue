<template lang="pug">
  #app
    h1 都道府県別の総人口推移
    PrefGraph
</template>

<script>

import PrefGraph from './components/PrefGraph'

export default {
  name: 'app',
  components: {
    PrefGraph,
  },
  data() {
    return {
    }
  },
}

</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.container {
  width: 100%;
  height: 400px;
}
.sfc {
  border: 2px solid skyblue;
}
</style>
