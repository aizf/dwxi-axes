<template>
  <svg :width="width" :height="height" class="ChartKeywordPath">
    <g :transform="`translate(25,25)`">
      <g class="keywordPath">
        <g v-for="path in $_keywordPath" :key="path.index">
          <path
            v-for="(coord,key) in path['part']"
            :d="generatePath(coord)"
            stroke="red"
            fill="transparent"
            stroke-width="5"
            :stroke-opacity="0.7"
            :key="key"
          />
        </g>
      </g>
      <g
        v-for="(vals,key,hIndex) in chartData"
        :transform="`translate(${hIndex*hPadding},0)`"
        :key="key"
      >
        <line
          :x1="0"
          :y1="0"
          :x2="0"
          :y2="(Object.keys(vals).length-1)*vPadding+5"
          stroke-width="5px"
          stroke="#aaa"
          stroke-opacity="0.7"
        />
        <text
          class="keywords"
          v-for="(val,keyword) in vals"
          :x="val.x"
          :y="val.y"
          text-anchor="start"
          :key="keyword"
        >{{keyword}}</text>
        <text
          class="key"
          x="0"
          :y="Object.keys(vals).length*vPadding+25"
          text-anchor="middle"
          font-weight="bold"
        >{{key}}</text>
      </g>
    </g>
  </svg>
</template>
<script>
import * as d3 from "d3";
export default {
  name: "ChartKeywordPath",
  props: {
    width: String,
    height: String
    // keywordPath: []
  },
  data() {
    return {
      axes_d3_csv: [],
      vPadding: 25, // 垂直Padding
      hPadding: 150, // 水平Padding
      keywordPath: [
        {
          time1: "april",
          time2: "hot",
          time3: "29th",
          time4: "march",
          time5: "amp"
        },
        {
          time1: "29th",
          time2: "march",
          time3: "april",
          time4: "amp",
          time5: "join"
        },
        {
          time1: "amp",
          time2: "29th",
          time3: "april",
          time4: "march",
          time5: "join"
        },
        {
          time1: "voice",
          time2: "save",
          time3: "join",
          time4: "march",
          time5: "silent"
        },
      ]
    };
  },
  computed: {
    axesNames() {
      return this.axes_d3_csv.length > 0 ? this.axes_d3_csv.columns : [];
    },
    chartData() {
      // 初始化data
      const data = {};
      this.axesNames.forEach(key => {
        data[key] = {};
      });
      // 添加数据
      Array.from(this.axes_d3_csv).forEach((d, i) => {
        this.axesNames.forEach(key => {
          data[key][d[key]] = { content: d[key], x: 0, y: i * this.vPadding };
        });
      });
      return data;
    },
    $_keywordCoord() {
      const _paths = [];
      // 添加path在每条轴的位置的x,y
      this.keywordPath.forEach((path, i) => {
        const eachPath = { index: i };
        this.axesNames.forEach((key, hIndex) => {
          // 当前关键词的信息
          const keyword = this.chartData[key][path[key]];
          eachPath[key] = {
            x: keyword.x + hIndex * this.hPadding,
            y: keyword.y
          };
        });
        _paths.push(eachPath);
      });
      return _paths;
    },
    $_keywordPath() {
      // 将$_keywordCoord的坐标转化为{x0,y0,x1,y1}格式
      const _paths = [];
      this.$_keywordCoord.forEach(path => {
        const eachPath = { index: path.index };
        eachPath["part"] = [];
        this.axesNames.forEach((key, i, arr) => {
          if (i === 0) return;
          eachPath["part"].push({
            x0: path[arr[i - 1]].x,
            y0: path[arr[i - 1]].y,
            x1: path[arr[i]].x,
            y1: path[arr[i]].y
          });
        });
        _paths.push(eachPath);
      });
      return _paths;
    }
  },
  methods: {
    generatePath(d) {
      return d3
        .linkHorizontal()
        .source(() => [d.x0, d.y0])
        .target(() => [d.x1, d.y1])();
    }
  },
  created() {
    d3.csv("./data/tttt.csv")
      .then(res => {
        // console.log(res);
        this.axes_d3_csv = res;
      })
      .catch(err => {
        throw new Error("找不到data文件");
      });
  }
};
</script>

<style scoped>
/* a {
  color: #42b983;
} */
.ChartKeywordPath .keywords,
.ChartKeywordPath .key {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  cursor: pointer;
  /* stroke: #2c3e50; */
}
</style>
