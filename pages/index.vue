<template>
  <v-container
    grid-list-md
    style="font-family: 'Ubuntu', mono;"
  >
    <h1> Crossfilter on Nuxt Example</h1>
    <v-layout
      id="layout"
      row
      wrap
    >
      <v-flex xs12>
        <div
          id="mystats"
          class="dc-data-count blue--text font-weight-black"
          style="float: left;"
        >
          <span
            class="filter-count"
            style="font-size:30px"
          /> (selected) / <span class="total-count" /> (total)
          <v-btn
            id="myreset3"
            dark
            small
            color="pink darken-1"
          >Rest</v-btn>
          <!-- <span class="font-weight-black blue--text"> | </span> -->
          <v-btn-toggle
            v-model="toggle_multiple"
            class="transparent"
            multiple
          >
            <v-btn
              v-for="(item,index) in NameList"
              :key="index"
              :value="item.name"
              :color="item.show === true ? 'pink darken-1' : 'grey darken-1'"
              light
              flat
              small
              @click.stop=" item.show === false ? ShowChart(item.name) : HideChart(item.name)"
            >{{ item.name }}</v-btn>
          </v-btn-toggle>
        </div>
      </v-flex>
    </v-layout>
    <v-divider />

    <transition-group
      name="slide-fade"
      tag="v-flex"
      class="layout row wrap"
    >
      <v-flex
        v-for="(item, index) in NameList"
        v-show="item.show"
        :key="index+1"
        xs12
        sm12
        md12
        lg6
      >
        <v-card
          class="elevation-4"
          height="250"
        >
          <span style="position:absolute;top:3px;left:10px;font-size:15px;font-weight:bold;">
            {{ item.title }}
          </span>
          <v-container
            :id="item.name + '-container'"
            justify-center
            fill-height
            style="padding-top:60px"
          >
            <div :id="item.name + '-chart'" />
          </v-container>
        </v-card>
      </v-flex>
    </transition-group>
    <!-- {{ data }} -->
    <TreeView
      :data="data"
      :filterdata="filterdata"
    />
  </v-container>
</template>

<script>
import axios from 'axios'
import * as dc from 'dc'
import * as d3 from 'd3'
import * as crossfilter from 'crossfilter'
import TreeView from '@/components/TreeView'

function csvtojson(csv) {
  var lines = csv.split('\n')
  var result = []
  var headers = lines[0].split(',')
  for (var i = 1; i < lines.length - 1; i++) {
    var obj = {}
    var currentline = lines[i].split(',')
    for (var j = 0; j < headers.length; j++) {
      obj[headers[j]] = currentline[j]
    }
    result.push(obj)
  }
  //return result; //JavaScript object
  return result //JSON
}

function sortBy(filed, rev, primer) {
  rev = rev ? -1 : 1
  return function(a, b) {
    a = a[filed]
    b = b[filed]
    if (typeof primer != 'undefined') {
      a = primer(a)
      b = primer(b)
    }
    if (a < b) {
      return rev * -1
    }
    if (a > b) {
      return rev * 1
    }
    return 1
  }
}

export default {
  asyncData({ req, params }) {
    return axios
      .get(
        'https://raw.githubusercontent.com/YuanTian1991/Scripts/master/mockdata.csv'
      )
      .then(res => {
        var tmpdata = csvtojson(res.data)
        tmpdata.forEach(function(d) {
          d['index'] = +d['index']
        })
        tmpdata.sort(sortBy('code_index', false, String))
        return { data: tmpdata, filterdata: tmpdata }
      })
      .catch(err => {
        return { data: '' }
      })
  },
  components: {
    TreeView: TreeView
  },
  data() {
    return {
      data: '',
      filterdata: [],
      ndx: '',
      ChartList: [],
      DimList: [],
      GroupList: [],
      NameList: {
        category: {
          name: 'category',
          show: true,
          chart: 'pie',
          title: 'What category are these Cell Lines ?',
          label: [
            '[Primary Tumour Tissue]',
            '[GBM Cell Line]',
            '[Engineered GBM Cell Line]',
            '[Xenograph Derive Cell Line]',
            '[Peripheral Blood Mononuclear Cell]',
            '[Patient Derived Neural Stem Cell]',
            '[Human Feotal Primary Tissue]',
            '[Human Neural Stem Cell Line]',
            '[Isogenic Gene Cell Line]'
          ],
          color: [
            '#a6cee3',
            '#1f78b4',
            '#b2df8a',
            '#33a02c',
            '#fb9a99',
            '#e31a1c',
            '#fdbf6f',
            '#ff7f00',
            '#cab2d6'
          ]
        },
        labs: {
          name: 'labs',
          show: true,
          chart: 'pie',
          title: 'Where are these Cell Lines cultured ?',
          label: ['Spain', 'UCL', 'Edinburgh'],
          color: ['#3288bd', '#e08214', '#dd3497']
        },
        epic: {
          name: 'epic',
          show: false,
          chart: 'pie',
          title: 'Does Cell Line have EPIC data ?',
          label: ['TRUE', 'FALSE'],
          color: ['#dd3497', '#1f78b4']
        },
        rna_seq: {
          name: 'rna_seq',
          show: true,
          chart: 'pie',
          title: 'Does Cell Line have RNA-seq data ?',
          label: ['TRUE', 'FALSE'],
          color: ['#dd3497', '#1f78b4']
        },
        wgs: {
          name: 'wgs',
          show: false,
          chart: 'pie',
          title: 'Does Cell Line have WGS data ?',
          label: ['TRUE', 'FALSE'],
          color: ['#dd3497', '#1f78b4']
        },
        atac_seq: {
          name: 'atac_seq',
          show: false,
          chart: 'pie',
          title: 'Does Cell Line have ATAC-seq data ?',
          label: ['TRUE', 'FALSE'],
          color: ['#dd3497', '#1f78b4']
        },
        index: {
          name: 'index',
          show: true,
          chart: 'bar',
          title: 'This is just a test for numeric variable...'
        }
      },
      text: 'center',
      toggle_multiple: []
    }
  },
  mounted() {
    for (var key in this.NameList) {
      if (this.NameList[key].show === true)
        this.toggle_multiple.push(this.NameList[key].name)
    }

    this.GenerateDC()
    for (var key in this.NameList) {
      switch (this.NameList[key].chart) {
        case 'pie':
          this.AddPie(this.NameList[key].name)
          break
        case 'bar':
          this.AddBar(this.NameList[key].name)
          break
        default:
          this.AddPie(this.NameList[key].name)
      }
    }
    this.SyncData()
  },
  methods: {
    GenerateDC() {
      var that = this
      that.ndx = crossfilter(this.data)

      // var tmpDim = that.ndx.dimension(function(d) {
      //   return d.id
      // })
      // var tmpGroup = tmpDim.group().reduceCount()
      // Total count
      var countChart = dc.dataCount('#mystats')
      countChart.dimension(that.ndx).group(that.ndx.groupAll())

      // // monitor all charts
      // dc.chartRegistry.list().forEach(function(chart) {
      //   chart.on('filtered', function() {
      //     that.data = that.DimList['categary'].top(Infinity)
      //   })
      // })

      // rest all figure
      d3.selectAll('#myreset3').on('click', function() {
        // that.ChartList['categary'].filterAll()
        for (var key in that.NameList) {
          that.ChartList[that.NameList[key].name].filterAll()
        }
        dc.redrawAll()
      })
      dc.renderAll()
    },
    AddPie(name) {
      // Add more Pie plot here
      var that = this

      that.ChartList[name] = dc.pieChart('#' + name + '-chart')
      that.DimList[name] = that.ndx.dimension(function(d) {
        return d[name]
      })
      that.GroupList[name] = that.DimList[name].group()

      let tmpwidth = document.getElementById('layout').offsetWidth

      if (name === 'category') {
        var tmpdiff = 240
      } else {
        var tmpdiff = 100
      }

      that.ChartList[name]
        .width(tmpwidth * 0.45)
        .controlsUseVisibility(true)
        .dimension(that.DimList[name])
        .group(that.GroupList[name])
        .colors(
          d3
            .scaleOrdinal()
            .domain(that.NameList[name].label)
            .range(that.NameList[name].color)
        )
        .cx(370)
        .legend(dc.legend().y(30))
        .renderLabel(true)
        .label(function(d) {
          return d.value
        })

      that.ChartList[name].on('pretransition', function(chart) {
        chart
          .selectAll('.dc-legend-item text')
          .text('')
          .append('tspan')
          .text(function(d) {
            return d.name
          })
          .append('tspan')
          .attr('x', tmpdiff)
          .attr('text-anchor', 'end')
          .text(function(d) {
            return d.data
          })
      })

      that.ChartList[name].filterAll()
      that.ChartList[name].render()
    },
    AddBar(name) {
      var that = this

      that.ChartList[name] = dc.barChart('#' + name + '-chart')
      that.DimList[name] = that.ndx.dimension(function(d) {
        return d[name]
      })
      that.GroupList[name] = that.DimList[name].group().reduceCount()

      var tmpwidth = document.getElementById(name + '-container').offsetWidth
      var tmpheight = document.getElementById(name + '-container').offsetHeight
      // var barChartheight = document.getElementById('box-test').offsetHeight
      let max = that.DimList[name].top(1)[0][name] + 1
      let min = that.DimList[name].bottom(1)[0][name]

      that.ChartList[name]
        .width(tmpwidth * 0.95)
        .height(tmpheight * 0.85)
        .controlsUseVisibility(true)
        .x(d3.scaleLinear().domain([min, max]))
        .yAxisLabel('Count')
        .xAxisLabel(name)
        .linearColors(['#dd3497', '#ffffbf', '#a50026'])
        .elasticY(true)
        .brushOn(true)
        // .elasticX(true)
        .dimension(that.DimList[name])
        .group(that.GroupList[name])

      that.ChartList[name].render()
    },
    SyncData() {
      // monitor all charts
      var that = this

      dc.chartRegistry.list().forEach(function(chart) {
        chart.on('filtered', function() {
          let tmpdata = that.DimList['category'].top(Infinity)
          tmpdata.sort(sortBy('code_index', false, String))
          that.filterdata = tmpdata
        })
      })
    },
    ShowChart(name) {
      this.NameList[name].show = true
    },
    HideChart(name) {
      this.NameList[name].show = false
    }
  }
}
</script>

<style>
@import '~/assets/css/dc.min.css';

.dc-chart g.chart-body {
  clip-path: none;
}
.dc-chart rect.stack-deselected {
  opacity: 0.2;
}
.slide-fade-enter-active {
  transition: all 0.5s ease;
}
.slide-fade-leave-active {
  transition: all 0.5s cubic-bezier(1, 0.5, 0.8, 1);
}
.slide-fade-enter, .slide-fade-leave-to
/* .slide-fade-leave-active below version 2.1.8 */ {
  transform: translateX(10px);
  opacity: 0;
}
</style>
