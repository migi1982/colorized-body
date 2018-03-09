<template>
  <section>
    <ul class="pager">
      <li v-for="n in pageLength" :key="n" :index="n" :class="{on: page === n}" @click="page = n">{{n}}</li>
    </ul>
    <table>
      <tbody>
        <tr>
          <th @click="sortBy('name')">
            name&nbsp;
            <span v-if="sortKey === 'name' && sortOrder === 1">▲</span>
            <span v-if="sortKey === 'name' && sortOrder === -1">▼</span>
          </th>
          <th @click="sortBy('bust')">
            bust&nbsp;
            <span v-if="sortKey === 'bust' && sortOrder === 1">▲</span>
            <span v-if="sortKey === 'bust' && sortOrder === -1">▼</span>
          </th>
          <th @click="sortBy('waist')">
            waist&nbsp;
            <span v-if="sortKey === 'waist' && sortOrder === 1">▲</span>
            <span v-if="sortKey === 'waist' && sortOrder === -1">▼</span>
          </th>
          <th @click="sortBy('hip')">
            hip&nbsp;
            <span v-if="sortKey === 'hip' && sortOrder === 1">▲</span>
            <span v-if="sortKey === 'hip' && sortOrder === -1">▼</span>
          </th>
          <th>color</th>
        </tr>
        <tr v-for="(item, index) in sortedList" :key="index" :index="index">
          <td>{{item.name}}</td>
          <td>{{item.bust}}</td>
          <td>{{item.waist}}</td>
          <td>{{item.hip}}</td>
          <td>
            <div :style="{backgroundColor: getColor(item)}"></div>
          </td>
        </tr>
      </tbody>
    </table>
  </section>
</template>

<script>
import axios from 'axios'

const PAGE_LIMIT = 200
export default {
  data () {
    return {
      sortKey: 'bust',
      sortOrder: -1,
      page: 1,
      list: []
    }
  },
  computed: {
    minBust () {
      return Math.min(...this.list.map(item => item.bust))
    },
    maxBust () {
      return Math.max(...this.list.map(item => item.bust))
    },
    minWaist () {
      return Math.min(...this.list.map(item => item.waist))
    },
    maxWaist () {
      return Math.max(...this.list.map(item => item.waist))
    },
    minHip () {
      return Math.min(...this.list.map(item => item.hip))
    },
    maxHip () {
      return Math.max(...this.list.map(item => item.hip))
    },
    sortedList () {
      let data = this.list
      let sortKey = this.sortKey
      let order = this.sortOrder || 1
      let slice = {
        start: PAGE_LIMIT * (this.page - 1),
        end: PAGE_LIMIT * this.page
      }
      // 並びかえ
      if (sortKey) {
        data = data.slice().sort((a, b) => {
          a = a[sortKey]
          b = b[sortKey]
          return (a === b ? 0 : a > b ? 1 : -1) * order
        })
      }
      // 特定のページの項目だけ返す
      return data.slice(slice.start, slice.end)
    },
    pageLength () {
      return Math.ceil(this.list.length / PAGE_LIMIT)
    }
  },
  methods: {
    getArrangedNum (min, max, value) {
      return ((value - min) * 255 / (max - min)) << 0
    },
    getColor (item) {
      let b = this.getArrangedNum(this.minBust, this.maxBust, item.bust)
      let w = this.getArrangedNum(this.minWaist, this.maxWaist, item.waist)
      let h = this.getArrangedNum(this.minHip, this.maxHip, item.hip)
      return `rgb(${b}, ${w}, ${h})`
    },
    sortBy (key) {
      this.sortKey = key
      this.sortOrder = this.sortOrder * -1
    }
  },
  created () {
    axios.get('/data.json').then(res => {
      this.list = res.data
    })
  }
}
</script>

<style>
/* http://meyerweb.com/eric/tools/css/reset/
   v2.0 | 20110126
   License: none (public domain)
*/

html, body, div, span, applet, object, iframe,
h1, h2, h3, h4, h5, h6, p, blockquote, pre,
a, abbr, acronym, address, big, cite, code,
del, dfn, em, img, ins, kbd, q, s, samp,
small, strike, strong, sub, sup, tt, var,
b, u, i, center,
dl, dt, dd, ol, ul, li,
fieldset, form, label, legend,
table, caption, tbody, tfoot, thead, tr, th, td,
article, aside, canvas, details, embed,
figure, figcaption, footer, header, hgroup,
menu, nav, output, ruby, section, summary,
time, mark, audio, video {
  margin: 0;
  padding: 0;
  border: 0;
  font-size: 100%;
  font: inherit;
  vertical-align: baseline;
}
/* HTML5 display-role reset for older browsers */
article, aside, details, figcaption, figure,
footer, header, hgroup, menu, nav, section {
  display: block;
}
body {
  line-height: 1;
}
ol, ul {
  list-style: none;
}
blockquote, q {
  quotes: none;
}
blockquote:before, blockquote:after,
q:before, q:after {
  content: '';
  content: none;
}
table {
  border-collapse: collapse;
  border-spacing: 0;
}

body {
  color: #333;
  padding: 20px;
}

ul.pager {
  display: flex;
  margin-bottom: 20px;
}
ul.pager li {
  text-align: center;
  height: 30px;
  width: 30px;
  line-height: 30px;
  border: 1px solid #333;
  margin: 0 5px;
  cursor: pointer;
  border-radius: 5px;
}
ul.pager li.on {
  background: #333;
  color: #FFF;
}
ul.pager li:hover {
  opacity: .8;
}
th,td {
  vertical-align: middle;
  padding: 5px 20px;
  border: 1px solid #333;
}
td:nth-child(1) {
  width: 170px;
}
td:nth-child(2),
td:nth-child(3),
td:nth-child(4) {
  text-align: right;
  width: 120px;
}
td div {
  height: 30px;
  width: 30px;
  border-radius: 30px;
  margin: 0 auto;
}
th {
  background: #333;
  color: #FFF;
  font-weight: bold;
  cursor: pointer;
}
th:last-child {
  cursor: auto;
}
</style>
