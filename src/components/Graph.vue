<template lang="pug">
  .graph
    #MyChart
</template>

<script>

import Highcharts from 'highcharts';

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
    series: {
      events: {
        afterAnimate: function () {
            console.log('afterAnimate');
        }
      }
    }
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
  data() {
    return {
      chart: undefined,
    }
  },
  props: {
    queue: {
      type: Object,
    }
  },
  watch: {

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
  watch: {
    queue: function(){
      console.log(this.queue);
      if(this.queue.isAdd) {
        const series = {
          id: this.queue.id,
          name: this.queue.name,
          data: this.queue.data,
        }
        this.addSeries(series);
        this.$emit('complete', this.queue.id);
      } else {
        this.removeSeriesById(this.queue.id);
      }
    },
  },
  mounted() {
    this.initGraph();
  },
}

</script>

<style lang="scss" scoped>
  #MyChart {
    border-top: 1px dashed black;
  }
</style>
