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
    marginTop: 100,
    marginLeft: 100,
    marginBottom: 100,
    marginRight: 120,
  },
  xAxis: {
    title: {
      text: '年度',
      align: 'high',
    },
  },
  yAxis: {
    title: {
      text: '人口数',
      align: 'high',
      rotation: 0,
      y: -40,
      offset: -5
    },
  },
  legend: {
    layout: 'vertical',
    align: 'right',
    verticalAlign: 'top',
    x: 0,
    y: 80,
    // margin: 100
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
