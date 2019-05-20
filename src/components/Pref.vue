<template lang="pug">
  .pref
    .heading: h2 都道府県
    .prefList
      PrefItem(
        v-for='pref in prefs',
        :key='pref.prefCode',
        :pref='pref',
        :state='false'
        @change='changeCheckBox'
      )
</template>

<script>

import axios from 'axios';

import PrefItem from './PrefItem';

const RESAS = {
  domain: 'https://opendata.resas-portal.go.jp/',
  endPoints: {
    prefectures: 'api/v1/prefectures',
    populations: 'api/v1/population/composition/perYear', // prefCode, cityCode
  },
  options: {
    headers: {
      'X-API-KEY': '04dBR517ftRFMGaoYnHETY4Qqv0c5lqG75ihWT9j',
    },
  },
};

export default {
	components: {
    PrefItem,
  },
  data() {
    return {
      prefs: [],
    }
  },
  methods: {
    async init() {
      this.prefs = await this.getPrefList();
    },
    async getPrefList() {
      const result = await axios.get(RESAS.domain + RESAS.endPoints.prefectures, RESAS.options)
        .then((res) => {
          // ここ綺麗にしたい
          // RESAS-APIでは成功時、statusCodeはundefined
          if (res.data.statusCode !== undefined) {
            console.log('RESAS-API ERROR: ' + res.data.statusCode);
          }

          return res.data.result;
        })
        .catch((e) => {
          console.error(e);
        });
      //console.log(result);
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
          // RESAS-APIでは成功時、statusCodeはundefined
          if (res.data.statusCode !== undefined) { // undefinedはWritableではないため直接比較可能。https://www.ecma-international.org/ecma-262/6.0/#sec-undefined
            console.log('RESAS-API ERROR: ' + res.data.statusCode);
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
    async changeCheckBox(e) {
      const targetPrefCode = parseInt(e.target.value, 10);
      if(e.target.checked) {
        // this.addPrefToGraph(targetPrefCode)
        const data = await this.getPopulationsByCode(targetPrefCode);
        const series = {
          id: targetPrefCode,
          name: this.getPrefNameByCode(targetPrefCode),
          data: data,
        }
        this.$emit('add', series);
      } else {
        this.$emit('remove', targetPrefCode);
      }
    },
  },
  mounted() {
    this.init();
  }
}

</script>

<style lang="scss" scoped>
  .pref {
    padding: 30px 50px;
  }
  .heading {
    text-align: left;
  }
  h2 {
    display: inline-block;
    border: 1px solid black;
    font-size: 1.2rem;
    padding: .1em .3em;
    letter-spacing: .05em;
  }
  .prefList {
    margin-top: 10px;
    min-height: 190px;
    display: flex;
    flex-wrap: wrap;
    justify-content: left;
  }
</style>
