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
    animation: false,
  },
  xAxis: {
    title: {
      text: '年度',
      align: 'high',
    },
    gridLineWidth: 1,
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
    itemMarginTop: 3,
    itemMarginBottom: 3,
  },

  plotOptions: {
    series: {
      animation: {
        duration: 0
      }
    },
  },
  series: [],
  tooltip: {
    animation: false,
  },
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
    queue: function(){
      if(this.queue.isAdd) {
        const series = {
          id: this.queue.seriesId,
          name: this.queue.seriesName,
          data: this.queue.seriesData,
        };
        // this.chart.options.plotOptions.series.events.afterAnimate = () => {
        //
        // };
        this.addSeries(series);
        this.$emit('complete', this.queue.seriesId);
      } else {
        this.removeSeriesById(this.queue.seriesId);
      }
    },
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
  #MyChart {
    border-top: 1px dashed black;
  }
</style>
