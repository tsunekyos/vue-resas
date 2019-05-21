<template lang="pug">
  .prefGraph
    .heading: h1 {{ title }}
    pref(@add="addSeries", @remove="removeSeriesById")
    #MyChart
</template>

<script>

import Pref from './Pref.vue'

import Highcharts from 'highcharts';
import 'highcharts/modules/no-data-to-display.js';

const options = {
  title: {
    text: '',
  },
  chart: {
    backgroundColor: '#f8f8f8',
    showAxes: true,
  },
  xAxis: {
    title: {
      text: '年度',
      align: 'high',
      margin: 50,
    },
  },
  yAxis: {
    title: {
      text: '人口数',
      align: 'high',
      rotation: 0,
      y: -10,
      margin: 50,
    },
  },
  legend: {
    layout: 'vertical',
    align: 'right',
    verticalAlign: 'top',
  },

  plotOptions: {
  },
  lang: {
    noData: "Nichts zu anzeigen"
  },
  noData: {
    style: {
      fontWeight: 'bold',
      fontSize: '15px',
      color: '#303030'
    }
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
      // ダブルクリック時にgetできない。できていないからか。それまで消せないようにするべきか
      const targetSeries = this.chart.get(seriesId);
      if(targetSeries !== undefined){
        targetSeries.remove();
      }
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
  #MyChart {
    border-top: 1px dashed black;
  }
</style>
