<template lang="pug">
  .prefGraph
    .heading: h1 {{ title }}
    pref(
      :prefs='prefs',
      @add="addSeriesByCode",
      @remove="removeSeriesById"
    )
    graph(:queue='queue', @complete='unlock')
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
        id: 0,
        name: '',
        data: [],
      },
      no: 0,
    }
  },
  props: {
    title: {
      type: String,
    }
  },
  methods: {
    async init() {
      const prefs = await this.getPrefList();
      this.prefs = prefs.map(pref => {
        pref.disabled = false;
        return pref;
      });
    },
    async getPrefList() {
      const result = await axios.get(RESAS.domain + RESAS.endPoints.prefectures, RESAS.options)
        .then((res) => {
          // 成功時、statusCodeはundefined
          if (res.data.statusCode !== undefined) { // undefinedはWritableではないため直接比較可能。https://www.ecma-international.org/ecma-262/6.0/#sec-undefined
            throw new Error(`RESAS-API ERROR: ${res.data.statusCode}`);
          }
          return res.data.result;
        })
        .catch((e) => {
          console.error(e);
        });
      return result;
    },
    async getPopulationsByCode(prefCode) {
      const myOptions = {
        headers: RESAS.options.headers,
        params: {
          prefCode: prefCode,
          cityCode: '-',
        }
      };
      const result = await axios.get(RESAS.domain + RESAS.endPoints.populations, myOptions)
        .then((res) => {
          // 成功時、statusCodeはundefined
          if (res.data.statusCode !== undefined) {
            throw new Error(`RESAS-API ERROR: ${res.data.statusCode}`);
          }

          const popsData = res.data.result.data[0].data.map(val => {
            return [ val.year, val.value ];
          });
          return popsData;
        })
        .catch((e) => {
          console.error(e);
        });
      return result;
    },
    getPrefNameByCode(prefCode) {
      return this.prefs.find(pref => pref.prefCode === prefCode).prefName;
    },
    createQueue(isAdd, id, name=null, data=null) {
      return {
        no: this.no++,
        isAdd: isAdd,
        id: id,
        name: name,
        data: data,
      };
    },
    async addSeriesByCode(prefCode) {
      this.prefs.find(pref => pref.prefCode === prefCode).disabled = true;

      const populationData = await this.getPopulationsByCode(prefCode);
      const prefName = this.getPrefNameByCode(prefCode);

      this.queue = this.createQueue(true, prefCode, prefName ,populationData);

    },
    removeSeriesById(id) {
      this.queue = this.createQueue(false, id);
    },
    unlock(id) {
      this.prefs.find(pref => pref.prefCode === id).disabled = false;
    }
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
