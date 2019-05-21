<template lang="pug">
  .prefGraph
    .heading: h1 {{ title }}
    pref
    graph
</template>

<script>

import Pref from './Pref.vue'
import Graph from './Graph.vue'

export default {
  components: {
    Pref,
    Graph,
  },
  props: {
    title: {
      type: String,
    }
  },
  data() {
    return {
    }
  },
  methods: {
  },
}

</script>

<style lang="scss" scoped>
  .prefGraph {
    border: 1px solid black;
    max-width: 880px;
    margin: 0 auto;
  }
  .heading {
    background-color: #f2f2f2;
  }
  h1 {
    padding: .3em 0;
    font-size: 1.5rem;
    letter-spacing: .05em;
  }
</style>
