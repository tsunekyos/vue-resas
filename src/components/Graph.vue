<template lang="pug">
  .graph
    p {{ series }}
    #MyChart
</template>

<script>

import Highcharts from 'highcharts';

const options = {
  title: {
    text: '都道府県別人口推移数'
  },
  chart: {
    backgroundColor: '#f2f2f2',
    showAxes: true,
  },
  yAxis: {
    title: {
      text: '人口数'
    }
  },
  legend: {
  },

  plotOptions: {
  },

  series: [],
}
/*

  id: prefCode,
  name: this.getPrefNameByCode(prefCode),
  data: popsData,
*/
export default {
  data() {
    return {
      chart: undefined,
    }
  },
  props: {
    ser: {
      type: Array,
    }
  },
  watch: {
    series: function(){
      this.series
      this.addSeries();
    },
  },
  methods: {
    init() {
      this.chart = Highcharts.chart('MyChart', options);
      this.addSeries({
        id: '112',
        name: 'fighter',
        data: [10, 20, 22, 33, 14, 0, 9, 5]
      })
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
      thic.chart.get(seriesId).remove();
    },
  },
  mounted() {
    this.init();
  }
}

</script>

<style lang="scss" scoped>

</style>
