<template lang="pug">
  #app
    PrefGraph(title="都道府県別人口推移グラフ")
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
  padding-bottom: 120px;
}
.container {
  width: 100%;
  height: 400px;
}
.sfc {
  border: 2px solid skyblue;
}
h1, h2 {
  margin: 0;
}
</style>
