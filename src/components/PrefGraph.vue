<template lang="pug">
  .prefGraph
    .heading: h1 {{ title }}
    pref(
      :prefs='prefs',
      @add="addSeriesByPrefCode",
      @remove="removeSeriesById"
    )
    graph(:queue='queue', @complete='changePrefBtnDisable($event, false)')
</template>

<script>

import axios from 'axios';

import Pref from './Pref.vue'
import Graph from './Graph.vue'

const RESAS = {
  domain: 'https://opendata.resas-portal.go.jp/',
  endPoints: {
    prefectures: 'api/v1/prefectures',
    populations: 'api/v1/population/composition/perYear',
  },
  options: {
    headers: {
      'X-API-KEY': '04dBR517ftRFMGaoYnHETY4Qqv0c5lqG75ihWT9j',
    },
  },
};

export default {
  components: {
    Pref,
    Graph,
  },
  data() {
    return {
      prefs: [],
      queue: {
        isAdd: true,
        seriesId: 0,
        seriesName: '',
        seriesData: [],
      },
    }
  },
  props: {
    title: {
      type: String,
    }
  },
  methods: {
    // 都道府県リストを取得して表示
    async init() {
      const prefs = await this.getPrefList();
      this.prefs = prefs.map(pref => {
        pref.disabled = false;
        return pref;
      });
    },
    // [{ prefCode, prefName }]
    async getPrefList() {
      return await axios.get(RESAS.domain + RESAS.endPoints.prefectures, RESAS.options)
        .then((res) => {
          // 成功時は、data.resultにArrayが入る
          if (res.data.result === undefined) { // undefinedはWritableではないため直接比較可能。https://www.ecma-international.org/ecma-262/6.0/#sec-undefined
            throw new Error(`RESAS-API ERROR: ${res.data.message || res.data}`);
          }
          return res.data.result;
        })
        .catch((e) => {
          console.error(e);
        });
    },

    // [[ 対象年, 人口数 ], [ 対象年, 人口数 ], ... ]
    async getPopulationsByPrefCode(prefCode) {
      const option = {
        headers: RESAS.options.headers,
        params: {
          prefCode: prefCode,
          cityCode: '-', // 全市を対象とする
        }
      };
      return await axios.get(RESAS.domain + RESAS.endPoints.populations, option)
        .then((res) => {
          // 成功時は、data.resultにArrayが入る
          if (res.data.result === undefined) {
            throw new Error(`RESAS-API ERROR: ${res.data.statusCode || res.data}`);
          }

          const populationData = res.data.result.data[0].data.map(val => {
            return [ val.year, val.value ];
          });

          return populationData;
        })
        .catch((e) => {
          console.error(e);
        });
    },
    getPrefNameByPrefCode(prefCode) {
      return this.prefs.find(pref => pref.prefCode === prefCode).prefName;
    },
    createQueue(isAdd, seriesId, seriesName=null, seriesData=null) {
      return {
        isAdd,
        seriesId,
        seriesName,
        seriesData,
      };
    },
    async addSeriesByPrefCode(prefCode) {
      this.changePrefBtnDisable(prefCode, true);

      const populationData = await this.getPopulationsByPrefCode(prefCode);
      const prefName = this.getPrefNameByPrefCode(prefCode);

      this.queue = this.createQueue(true, prefCode, prefName, populationData);

    },
    removeSeriesById(id) {
      this.queue = this.createQueue(false, id);
    },
    changePrefBtnDisable(prefCode, beDisabled) {
      this.prefs.find(pref => pref.prefCode === prefCode).disabled = beDisabled;
    },
  },
  mounted() {
    this.init();
  }
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
