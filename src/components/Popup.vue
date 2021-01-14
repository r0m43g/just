<template lang="pug">
.ctrl
  button.btn(v-if="records.length < 1" @click.prevent="getRecords") Gauti informaciją
  button.btn(v-if="records.length > 0" @click="showRoutes") Maršrutai
  button.btn(v-if="records.length > 0" @click="showTable") Kelionlapų lentelė
  table.tbl(v-if="records.length > 0 && showingTable")
    tbody
      tr(v-for="rec in table")
        td {{ rec.route }}
        td
        td
        td {{ rec.name }}
  table.tbl(v-if="records.length > 0 && showingRoutes")
    tbody
      tr(v-for="(rec) in routes")
        td(@click="details(rec)")
          strong {{ rec.route }}
        td {{ rec.start }}
        td {{ rec.end }}
        td {{ rec.name }}
        td {{ rec.train }}
        td 
          input(type="checkbox", v-model="rec.show")
</template>

<script>
import recs from '@/data/data.js'
import { DateTime } from "luxon";
export default {
  name: 'Popup',
  data: () => ({
    records: [],
    showingTable: false,
    showingRoutes: false
  }),
  computed: {
    table() {
      return this.records.sort((a, b) => {
        return new DateTime.fromISO(a.start) - new DateTime.fromISO(b.start)
      }).filter( item => item.show == true)
    },
    routes() {
      return this.records.sort((a, b) => {
        return new DateTime.fromISO(a.start) - new DateTime.fromISO(b.start)
      })
    }
  },
  methods: {
    getRecords() {
      // chrome.tabs.query({ active: true, currentWindow: true }, (tabs) => {
      //   chrome.tabs.sendMessage(tabs[0].id, {greeting: "hello"}, response => {
      //     console.log('farewell: ' + response.farewell, 'resp: ' + response.resp);
      //     this.noop(response.resp)
      //   })
      // })
      this.noop(recs)
    },
    noop(recs) {
      recs.forEach(element => {
        let names = element.names.trim().split(',')
        if (names.length < 1 ) return
        let times = element.wtime.trim().split('-')
        let train = ''
        let thread = element.thread
        let route = this.getRoute(thread)
        
        names = names.filter( val => {
          if (/ER.*|EJ575.*/gm.test(val)){
            train = val
            // console.log('train: ', val)
          }
          let condition = (val !== train && val !== '')
          // console.log(val, condition ? 'passed' : 'didn\'t')
          return condition
        })
        names.forEach( name => {
          this.pushRecord(route, name, times[0].trim(), times[1].trim(), train, thread)
        })

      });
      console.log('Records: ' + this.records.length)
    },
    pushRecord(route, name, start, end, train, thread) {
      this.records.push({
        route,
        name,
        start,
        end,
        train,
        show: true,
        thread
      })
    },
    getRoute(thread) {
      let route;
      if (/Paranga.*/gm.test(thread[0].time)) return 'Paranga'
      for (let i = 0; i < thread.length; i++) {
        let str = thread[i].route
        if (/^\d\d\d$/gm.test(str)) {
          route = str
          break
        }
      }
      return route
    },
    showRoutes() {
      this.showingTable = false
      this.showingRoutes = true
    },
    showTable () {
      this.showingTable = true
      this.showingRoutes = false
    },
    details(rec) {
      let thread = [];
      rec.thread.forEach( el => {
        let reg = /\d\d:\d\d-\d\d:\d\d/gm
        let strTime = reg.exec(el.time)[0]
        let from = ''
        let till = ''
        if (strTime) {
          [from, till] = strTime.split('-')
          from = new DateTime.fromISO(from)
          till = new DateTime.fromISO(till)
        }
        thread.push ({
          from,
          till,
          type: el.route,
        })
      })
      console.log(thread)
    }
  }
}
</script>
<style scoped lang="sass">
.btn
  background: #fff
  color: #e80
  border: thin solid #e80
  padding: 10px 20px
  outline: none
  margin: 10px
  border-radius: 3px
  transition: background .3s, border .3s, color .3s
  &:hover
    color: #fff
    border: thin solid transparent
    background: #e80
  &:active
    border: thin solid #e80
    background: #fff
    color: #e80
.tbl
  border: thin solid #e80
  margin: 0 auto
  border-collapse: collapse
  & tr:nth-child(odd)
    background: #fdb

  & td
    padding: 5px 20px
    color: #c60

</style>
