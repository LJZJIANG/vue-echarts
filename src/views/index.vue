<!--
 * @Descripttion: 描述
 * @Author: ljz
 * @Date: 2020-06-13 12:54:21
 * @LastEditors: ljz
 * @LastEditTime: 2020-06-14 13:51:33
--> 
<template>
  <div class="content">
    <div class="left-menu">
      <ul class="menu">
        <li>全国疫情统计</li>
        <li class="active">武汉疫情统计</li>
        <li>全国疫情分析222</li>
        <li>关于疫情</li>
      </ul>
    </div>
    <div class="right-content">
      <div id="map"></div>
      <div>
        <div id="sistogram-add"></div>
        <div id="sistogram-total"></div>
      </div>
    </div>
  </div>
</template>

<script>
import echarts from "echarts";
import { times, addPerson, totalPerson } from "../data/personData";
let geoJson = require("../data/wuhan.json");
const colorList = {
  C_0: "#fff",
  C_9: "#F6E2BC",
  C_99: "#D97B62",
  C_499: "#BB3835",
  C_999: "#991520",
  C_10000: "#4F1116",
  C_10000_: "#290A0D"
};
export default {
  name: "",
  data() {
    return {
      background: "#ccc",
      currentItemName: ""
    };
  },
  mounted() {
    this.initEcharts();
  },
  methods: {
    initEcharts() {
      this.$nextTick(() => {
        this.myChart1 = echarts.init(document.getElementById("map"));
        this.myChart2 = echarts.init(document.getElementById("sistogram-add"));
        this.myChart3 = echarts.init(
          document.getElementById("sistogram-total")
        );
        this.myChart2.on("click", this.handleClickItem);
        this.myChart3.on("click", this.handleClickItem);
        echarts.registerMap("武汉市", geoJson); //#2

        this.option = {
          title: {
            text: "武汉市疫情图",
            padding: [0, 0, 80, 100],
            textStyle: { color: "#fff" }
          },
          series: [
            {
              name: "my custom map",
              type: "map",
              roam: true,
              map: "武汉市", //使用
              label: {
                normal: {
                  show: true
                },
                emphasis: {
                  show: true
                }
              },
              iaspectScale: 0.75,
              zoom: 1,
              itemStyle: {
                normal: {
                  borderWidth: 0.5, //区域边框宽度
                  borderColor: "#009fe8", //区域边框颜色
                  areaColor: this.background //区域颜色
                }
              }
            }
          ]
        };

        this.option1 = {
          title: {
            text: "新增确诊",
            textStyle: { color: "#fff" }
          },
          tooltip: {},
          xAxis: {
            data: times,
            axisLabel: {
              color: "#fff"
            }
          },
          yAxis: {
            axisLabel: {
              color: "#fff"
            }
          },
          dataZoom: [
            {
              type: "inside",
              show: true,
              xAxisIndex: [0],
              left: "9%",
              bottom: -5,
              start: 10,
              end: 90
            }
          ],
          series: [
            {
              name: "新增确诊",
              type: "bar",
              data: addPerson,
              itemStyle: {
                normal: {
                  color: params => {
                    return this.setBarItemColor(params);
                  }
                }
              }
            }
          ]
        };

        this.option2 = {
          title: {
            text: "累积确诊",
            textStyle: { color: "#fff" }
          },
          tooltip: {},
          xAxis: {
            data: times,
            axisLabel: {
              color: "#fff"
            }
          },
          dataZoom: [
            {
              type: "inside",
              show: true,
              xAxisIndex: [0],
              left: "9%",
              bottom: -5,
              start: 10,
              end: 90
            }
          ],
          yAxis: {
            axisLabel: {
              color: "#fff"
            }
          },
          series: [
            {
              name: "累积确诊",
              type: "bar",
              data: totalPerson,
              itemStyle: {
                normal: {
                  color: params => {
                    return this.setBarItemColor(params);
                  }
                }
              }
            }
          ]
        };

        this.myChart1.setOption(this.option, true);
        this.myChart2.setOption(this.option1, true);
        this.myChart3.setOption(this.option2, true);
      });
    },
    handleClickItem(params) {
      let { data, name, seriesName } = params;
      if (seriesName !== "新增确诊") {
        data = addPerson[params.dataIndex];
      }
      if (data < 1) {
        this.background = colorList.C_0;
      } else if (data <= 9) {
        this.background = colorList.C_9;
      } else if (data <= 99) {
        this.background = colorList.C_99;
      } else if (data <= 499) {
        this.background = colorList.C_499;
      } else if (data <= 999) {
        this.background = colorList.C_999;
      } else if (data <= 10000) {
        this.background = colorList.C_10000;
      } else if (data > 10000) {
        this.background = colorList.C_10000_;
      }

      this.currentItemName = name;
      this.initEcharts();
    },
    setBarItemColor(params) {
      let colors = times.map(item => {
        if (this.currentItemName === params.name) {
          return "#AE5F16";
        }
        return "#A20F1C";
      });
      return colors[params.dataIndex];
    }
  }
};
</script>

<style lang="less" scoped>
.content {
  display: flex;
  justify-content: space-between;
  height: calc(100vh - 80px);
  overflow: hidden;
  .left-menu {
    flex-basis: 180px;
    background: #343b42;
    .menu {
      padding-left: 0;
      li {
        cursor: pointer;
        list-style: none;
        height: 60px;
        line-height: 60px;
        text-align: center;
        color: #fff;
        &:hover {
          color: rgb(221, 135, 22);
        }
        &.active {
          color: rgb(221, 135, 22);
        }
      }
    }
  }
  .right-content {
    flex: 1;
    display: flex;
    justify-content: space-between;
    background: url("../assets/bg.jpg") no-repeat;
    background-size: 100% 100%;
    padding-top: 40px;
    #map {
      width: 650px;
      height: 700px;
    }
    > :last-of-type {
      display: flex;
      flex-direction: column;
      #sistogram-add,
      #sistogram-total {
        width: 550px;
        height: 400px;
        margin-bottom: 40px;
      }
    }
  }
}
</style>
