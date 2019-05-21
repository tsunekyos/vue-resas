<template lang="pug">
  .pref
    .heading: h2 都道府県
    .prefList
      PrefItem(
        v-for='pref in prefs',
        :key='pref.id'
        :pref='pref',
        @change='changeCheckBox'
      )
</template>

<script>

import PrefItem from './PrefItem';
export default {
	components: {
    PrefItem,
  },
  data() {
    return {
    }
  },
  props: {
    prefs: {
      type: Array,
    }
  },
  methods: {
    async changeCheckBox(e) {
      const targetPrefCode = parseInt(e.target.value, 10);
      if(e.target.checked) {
        this.$emit('add', targetPrefCode);
      } else {
        this.$emit('remove', targetPrefCode);
      }
    },
  },
}

</script>

<style lang="scss" scoped>
  .pref {
    padding: 30px 50px;
  }
  .heading {
    text-align: left;
  }
  h2 {
    display: inline-block;
    border: 1px solid black;
    font-size: 1.2rem;
    padding: .1em .3em;
    letter-spacing: .05em;
  }
  .prefList {
    margin-top: 10px;
    min-height: 190px;
    display: flex;
    flex-wrap: wrap;
    justify-content: left;
  }
</style>
