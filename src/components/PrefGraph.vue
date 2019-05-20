<template lang="pug">
  .prefGraph
    .heading: h1 {{ title }}
    pref(@add="addSeries", @remove="removeSeriesById")
    #MyChart
</template>

<script>

import Pref from './Pref.vue'

import Highcharts from 'highcharts';

const options = {
  title: {
    //text: '都道府県別人口推移数',
    text: 'test',
  },
  chart: {
    backgroundColor: '#f2f2f2',
    showAxes: true,
  },
  yAxis: {
    title: {
      text: '人口数',
    },
  },
  legend: {
  },

  plotOptions: {
  },

  series: [],
}

export default {
  components: {
    Pref,
  },
  props: {
    title: {
      type: String,
    }
  },
  data() {
    return {
      chart: undefined,
    }
  },
  methods: {
    initGraph (){
      this.chart = Highcharts.chart('MyChart', options);
    },
    addSeries(seriesObj){
      const series = {
        id: seriesObj.id,
        name: seriesObj.name,
        data: seriesObj.data,
      };
      this.chart.addSeries(series);
    },
    removeSeriesById(seriesId) {
      this.chart.get(seriesId).remove();
    },
  },
  mounted() {
    this.initGraph();
  },
}

</script>

<style lang="scss" scoped>
  .prefGraph {
    border: 1px solid black;
  }
  .heading {
    // border: 1px dashed red;
    background-color: #f2f2f2;
  }
  h1 {
    margin: 0;
  }
</style>
