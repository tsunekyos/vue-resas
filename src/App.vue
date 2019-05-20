<template lang="pug">
  #app
    h2 SFC
    PrefGraph

    h2 Single
    .prefs
      label(v-for="pref in prefs")
        input(type='checkbox', :value='pref.prefCode', v-model='checkedPrefs', @change='changeCheckBox')
        |[{{pref.prefCode}}: {{pref.prefName}}]
    #container
</template>

<script>

import PrefGraph from './components/PrefGraph'

import axios from 'axios'
import Highcharts from 'highcharts'

const RESAS = {
  domain: 'https://opendata.resas-portal.go.jp/',
  endPoints: {
    prefectures: 'api/v1/prefectures',
    populations: 'api/v1/population/composition/perYear',  // prefCode, cityCode
  },
  options: {
    headers: {
      'X-API-KEY': '04dBR517ftRFMGaoYnHETY4Qqv0c5lqG75ihWT9j',
    },
  },
};

export default {
  name: 'app',
  components: {
    PrefGraph,
  },
  data() {
    return {
      msg: '',
      prefs: [],// { prefCode, prefName }
      pops: [], //
      checkedPrefs: [],
      chart: undefined,
    }
  },
  methods: {
    // 都道府県チェックボックスに変更があった時
    changeCheckBox(e){
      const targetPrefCode = parseInt(e.target.value);
      if(e.target.checked) {
        this.addPrefToGraph(targetPrefCode)
      } else {
        this.removePrefFromGraph(targetPrefCode);
      }
    },
    // chartに都道府県データを追加
    async addPrefToGraph(prefCode){
      const popsData = await this.getPops(prefCode);
      // console.log(popsData);
      this.chart.addSeries({
        id: prefCode,
        name: this.getPrefNameByCode(prefCode),
        data: popsData,
      });
    },
    // chartから都道府県データを追加
    removePrefFromGraph(prefCode) {
      this.chart.get(prefCode).remove(); // idはprefCodeに等しい
    },
    // 都道府県データ取得
    async getPrefs() {
      const result = await axios.get(RESAS.domain + RESAS.endPoints.prefectures, RESAS.options)
        .then((res) => {
          // RESAS-APIでは成功時、statusCodeはundefined
          if (res.data.statusCode !== undefined) { // undefinedはWritableではないため直接比較可能。https://www.ecma-international.org/ecma-262/6.0/#sec-undefined
            console.log('RESAS-API ERROR: ' + response.data.statusCode);
            // throw response.data;
          }

          return res.data.result;
        })
        .catch((e) => {
          console.error(e);
        });
      //console.log(result);
      return result;
    },
    // prefCodeに基づいて人口を返す
    async getPops(prefCode) {
      const myOptions = {
        headers: RESAS.options.headers,
        params: {
          prefCode: prefCode,
          cityCode: '-',
        }
      };
      const result = await axios.get(RESAS.domain + RESAS.endPoints.populations, myOptions)
        .then((res) => {
          // RESAS-APIでは成功時、statusCodeはundefined
          if (res.data.statusCode !== undefined) { // undefinedはWritableではないため直接比較可能。https://www.ecma-international.org/ecma-262/6.0/#sec-undefined
            console.log('RESAS-API ERROR: ' + response.data.statusCode);
            // throw response.data;
          }

          console.log(res);
          // 人口数のみ抽出
          const popsData = res.data.result.data[0].data.map(val => {
            return val.value;
          });
          return popsData;
        })
        .catch((e) => {
          console.error(e);
        });
      return result;
    },
    initChart() {

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

        series: [
        ],
      }
      this.chart = Highcharts.chart('container', options);
    },
    getPrefNameByCode(prefCode) {
      return this.prefs.find(pref => pref.prefCode === prefCode).prefName;
    },
    async setPrefs(){
      this.prefs = await this.getPrefs();
    },
  },

  mounted() {
    console.log('... ******* ...');
    console.log('... mounted ...');
    this.setPrefs();
    this.initChart();
  }
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
}
.container {
  width: 100%;
  height: 400px;
}
.sfc {
  border: 2px solid skyblue;
}
</style>
