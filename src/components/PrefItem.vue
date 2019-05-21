<template lang="pug">
  .prefItem
      label
        input(
          type='checkbox',
          :value='pref.prefCode',
          :disabled='pref.disabled',
          @change="change",
        )
        |{{pref.prefName}}
</template>

<script>

export default {
  data() {
    return {
    }
  },
  props: {
    pref: {
      type: Object,
      required: true,
    },
  },
  methods: {
    change(e) {
      this.$emit('change', e);
    },
  },
}

</script>

<style lang="scss" scoped>
  label {
    cursor: pointer;
  }
  input {
    cursor: pointer;
  }
  .prefItem {
    text-align: left;
    min-width: 5.5em;
    margin: .25em 1em .25em 0;
  }
</style>
