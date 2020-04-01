<template>
  <div class="hello">
    <!-- 初始化echarts需要个有宽高的盒子 -->
    <div ref='mapbox' style="height:600px;width:900px"></div>
  </div>
</template>

<script>
import echarts from 'echarts'
import 'echarts/map/js/china.js'
import jsonp from 'jsonp'

// 使用地图前,需要先注册地图
const option = {
  title: {
    text: '2020年中国疫情地图',
    link: 'https://www.baidu.com',
  },
  series: [{
    name: '确诊人数',
    type: 'map', // 告诉echarts要去渲染地图
    map: 'china', // 告诉echarts要去渲染中国地图
    label: {
      // 控制对应地区的汉字
      show: true,
      color: '#333',
      fontSize: 10,
    },
    itemStyle: {
      // 地图板块的颜色
      areaColor: '#eee',
    },
    roam: true,
    zoom: 1.2, // 控制地图的放大缩小
    emphasis: {
      // 控制鼠标划过之后的背景色和字体颜色
      label: {
        color: '#fff',
        fontSize: 12
      },
      itemStyle: {
        areaColor: '#83b5e7'
      }
    },
    data: [] // 用来展示后台给的数据 {name:xx,value:xxx}
  }],
  visualMap: [{
    type: 'piecewise',
    show: true,
    // splitNumber: 4
    pieces: [
      // 分段
      {min: 10000},
      {min: 1000, max: 9999},
      {min: 100, max: 999},
      {min: 10, max: 99},
      {min: 1, max: 9},
    ],
    // align: 'right' // 默认left
    // orient: 'horizontal'
    // left right 这些属性控制分段所在的位置
    // showLabel: false,
    // textStyle: {}
    inRange: {
      symbol: 'rect',
      color: ['#ffc0b1', '#9c0505']
    },
    itemWidth: 20,
    itemHeigt: 10,
  }],
  tooltip: {
    trigger: 'item'
  },
  toolbox: {
    show: true,
    orient: 'vertical',
    left: 'right',
    top: 'center',
    feature: {
        dataView: {readOnly: false},
        restore: {},
        saveAsImage: {}
    }
  },
}

export default {
  name: 'HelloWorld',
  mounted() {
    this.getData(); // 
    this.mychart = echarts.init(this.$refs.mapbox);
    this.mychart.setOption(option);
  },
  methods: {
    getData() {
      jsonp('https://interface.sina.cn/news/wap/fymap2020_data.d.json?_=1580892522427',{}, (err,data)=>{
        if(!err) {
          console.log(data);
          let list = data.data.list.map(item => ({
            name: item.name,
            value: item.value,
          }));
          option.series[0].data = list;
          this.mychart.setOption(option);
          // 这行代码能执行的前提是DOM已经渲染完成，只有DOM渲染完成才能执行echarts渲染
        }
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.hello{
  display: flex;
  justify-content: center;
}
</style>
