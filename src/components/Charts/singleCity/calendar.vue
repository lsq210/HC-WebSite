<template>
  <div :class="className" :id="id" :style="{height:height,width:width}"></div>
</template>

<script>
/* eslint-disable */
import echarts from "echarts";
import resize from "../mixins/resize";
import { getYearAqiData } from "../../../api/data/main";
import axios from "axios";

export default {
  mixins: [resize],
  props: {
    className: {
      type: String,
      default: "chart"
    },
    id: {
      type: String,
      default: "chart"
    },
    width: {
      type: String,
      default: "200px"
    },
    height: {
      type: String,
      default: "200px"
    }
  },
  data() {
    return {
      chart: null
    };
  },
  mounted() {
    this.initChart();
  },
  beforeDestroy() {
    if (!this.chart) {
      return;
    }
    this.chart.dispose();
    this.chart = null;
  },
  methods: {
    initChart() {
      this.chart = echarts.init(document.getElementById(this.id));

      function getVirtulData(year) {
        year = year || "2018";
        var date = +echarts.number.parseDate(year + "-01-01");
        var end = +echarts.number.parseDate(+year + 1 + "-01-01");
        var dayTime = 3600 * 24 * 1000;
        var data = [];
        for (var time = date; time < end; time += dayTime) {
          data.push([
            echarts.format.formatTime("yyyy-MM-dd", time),
            Math.floor(Math.random() * 10000)
          ]);
        }
        // alert(JSON.stringify(data))
        return data;
      }

      var data = getVirtulData(2017);

    //   axios
    //     .get("http://120.77.180.246:8070/getyearaqidata", {
    //       params: {
    //         format: "json",
    //         city: 2,
    //         year: 2017
    //       }
    //     })
    //     .then(function(response) {
    //       alert(response);
    //     })
    //     .catch(function(error) {
    //       console.log(error);
    //     });

    var data = getYearAqiData({
        format: "json",
        city: 2,
        year: 2017
    })

      var option = {
        backgroundColor: "#404a59",

        title: {
          top: 30,
          text: "2017年武汉每天的AQI指数",
          left: "center",
          textStyle: {
            color: "#fff"
          }
        },
        tooltip: {
          trigger: "item"
        },
        legend: {
          top: "30",
          left: "100",
          data: ["AQI指数", "Top 12"],
          textStyle: {
            color: "#fff"
          }
        },
        calendar: [
          {
            top: 100,
            left: "center",
            range: ["2017-01-01", "2017-06-30"],
            splitLine: {
              show: true,
              lineStyle: {
                color: "#000",
                width: 4,
                type: "solid"
              }
            },
            yearLabel: {
              formatter: "{start}  1st",
              textStyle: {
                color: "#fff"
              }
            },
            itemStyle: {
              normal: {
                color: "#323c48",
                borderWidth: 1,
                borderColor: "#111"
              }
            }
          },
          {
            top: 340,
            left: "center",
            range: ["2017-07-01", "2017-12-31"],
            splitLine: {
              show: true,
              lineStyle: {
                color: "#000",
                width: 4,
                type: "solid"
              }
            },
            yearLabel: {
              formatter: "{start}  2nd",
              textStyle: {
                color: "#fff"
              }
            },
            itemStyle: {
              normal: {
                color: "#323c48",
                borderWidth: 1,
                borderColor: "#111"
              }
            }
          }
        ],
        series: [
          {
            name: "AQI指数",
            type: "scatter",
            coordinateSystem: "calendar",
            data: data,
            symbolSize: function(val) {
              return val[1] / 500;
            },
            itemStyle: {
              normal: {
                color: "#ddb926"
              }
            }
          },
          {
            name: "AQI指数",
            type: "scatter",
            coordinateSystem: "calendar",
            calendarIndex: 1,
            data: data,
            symbolSize: function(val) {
              return val[1] / 500;
            },
            itemStyle: {
              normal: {
                color: "#ddb926"
              }
            }
          },
          {
            name: "Top 12",
            type: "effectScatter",
            coordinateSystem: "calendar",
            calendarIndex: 1,
            data: data
              .sort(function(a, b) {
                return b[1] - a[1];
              })
              .slice(0, 12),
            symbolSize: function(val) {
              return val[1] / 500;
            },
            showEffectOn: "render",
            rippleEffect: {
              brushType: "stroke"
            },
            hoverAnimation: true,
            itemStyle: {
              normal: {
                color: "#f4e925",
                shadowBlur: 10,
                shadowColor: "#333"
              }
            },
            zlevel: 1
          },
          {
            name: "Top 12",
            type: "effectScatter",
            coordinateSystem: "calendar",
            data: data
              .sort(function(a, b) {
                return b[1] - a[1];
              })
              .slice(0, 12),
            symbolSize: function(val) {
              return val[1] / 500;
            },
            showEffectOn: "render",
            rippleEffect: {
              brushType: "stroke"
            },
            hoverAnimation: true,
            itemStyle: {
              normal: {
                color: "#f4e925",
                shadowBlur: 10,
                shadowColor: "#333"
              }
            },
            zlevel: 1
          }
        ]
      };
      this.chart.setOption(option, true);
    }
  }
};
</script>
