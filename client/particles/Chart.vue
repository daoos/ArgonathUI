<template>
  <div class="chart">
    <div class="pull-right">
      <template v-for="column in columns">
        <span v-if="$index!==0" :style="{color: data.datasets[$index - 1].strokeColor}">
          <input type="checkbox" :value="$index" :id="column['key']" v-model="checked">
          <label :for="column['key']">{{column['label']}}</label>
        </span>
      </template>
    </div>
    <div :id="'legend-'+id" class="chart-legend pull-right"></div>
    <canvas :id="id" :style="{width:width + 'px', height:height + 'px'}"></canvas>
  </div>
</template>

<script>
  var Chart = require("chart.js")
  export default {
    data () {
      return {
        checked: [],
        chart: null,
        renderData: {},
        //options:{ legendTemplate : "<ul class=\"<%=name.toLowerCase()%>-legend\"><% for (var i=0; i<datasets.length; i++){%><li><span style=\"background-color:<%=datasets[i].strokeColor%>\"></span><%if(datasets[i].label){%><%=datasets[i].label%><%}%></li><%}%></ul>"}
      }
    },
    props: {
      id: String,
      height: {
        type: Number,
        default: 300
      },
      width: {
        type: Number,
        default: 400
      },
      columns: Array,
      type: {
        type: String,
        default: 'line' // line, bar, pie
      },
      data: {
        type: Object,
        default: {}
      }
    },
    ready () {
      var chartCanvas = document.getElementById(this.id).getContext("2d")
      var chart = new Chart(chartCanvas)
      switch (this.type) {
        case "line":
          // this.chart = chart.Line(this.data, this.options);
          this.chart = chart.Line(this.data);
          break;
        case "bar":
          // this.chart = chart.Bar(this.data, this.options)
          this.chart = chart.Bar(this.data)
          break;
        case "pie":
          // this.chart = chart.Pie(this.data, this.options)
          this.chart = chart.Pie(this.data)
          break;
      }
      // document.getElementById('legend-'+this.id).innerHTML = this.chart.generateLegend();
    },
    methods: {
    },
    watch: {
      'checked': function (val, oldVal) {
        this.renderData = {}
        this.renderData['labels'] = this.data['labels']
        this.renderData['datasets'] = []
        for (let index of this.checked) {
          this.renderData['datasets'].push(this.data['datasets'][index-1])
        }
        this.chart.destroy()
        var chartCanvas = document.getElementById(this.id).getContext("2d")
        var chart = new Chart(chartCanvas)
        if (this.checked.length) {
          if (this.type === 'bar') {
            this.chart = chart.Bar(this.renderData)
          } else {
            this.chart = chart.Line(this.renderData)
          }
        } else {
          if (this.type === 'bar') {
            this.chart = chart.Bar(this.data)
          } else {
            this.chart = chart.Line(this.data)
          }
        }
      }
    }
  }
</script>

<style>
.chart-legend ul {
  list-style-type: none;
  padding: 0px;
}
.chart-legend li {
  float: right;
  margin: 5px;
  margin-left: 0px;
}
.chart-legend li span{
  display: inline-block;
  width: 12px;
  height: 12px;
  margin-right: 5px;
}
</style>
