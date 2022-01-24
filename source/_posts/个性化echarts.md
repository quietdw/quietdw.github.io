---
title: 个性化 echarts 图表
date: 2019-06-29 00:00:00
updated: 2019-06-29 00:00:00
tags:
  - echarts
---

最近公司开发 ERP 系统，其中有表格展示，一开始也没放在心上，心想用 echarts 就行了，但真正开始做后，才发现 echarts 并不简单。

<!-- more -->

其中有一张表的需求是这样的：横坐标表示门店，纵坐标表示门店的销售额和预期销售额，超过预期，超过的部分用紫色表示，未达到预期，未完成的部分用红色表示。

由于对 echarts 不熟悉，一开始就想用堆叠柱状图表示，一部分是实际完成的销售额，一部分是超过的数据，超过的数据有正负，正值表示超过预期了，负值表示低于预期，结果展示出来是这样的

配置如下：

```javascript
option = {
  xAxis: {
    type: 'category',
    data: ['shop1', 'shop2', 'shop3', 'shop4', 'shop5', 'shop5', 'shop6']
  },
  yAxis: {
    type: 'value',
    axisLabel: {
      formatter(value) {
        return `${value} 万元`
      }
    }
  },
  tooltip: {
    trigger: 'axis'
  },
  series: [
    {
      name: '预期销售额',
      stack: 'one',
      data: [120, 200, 150, 80, 70, 110, 130],
      type: 'bar'
    },
    {
      name: '完成情况',
      stack: 'one',
      data: [12, 20, 15, -8, 7, -11, 13],
      type: 'bar',
      itemStyle: {
        color: function (p) {
          return p.value >= 0 ? 'purple' : 'red'
        }
      }
    }
  ]
}
```

展示出来的效果：

![初始图表](https://i.loli.net/2019/06/29/5d1712ca9761120404.png)

发现了几个问题，

1. 完成情况的值，应该是预期目标值+超过的数据，而不只是超出的数据；
2. 色块的位置，应该都在顶部的；
3. 两个色块叠加的高度，应该等于实际完成的销售额。

基于此，分析数据，然后得到解决方法：

1. 判断超出数据的值，如果为负，则预期目标值为（预期目标值-未达到的值），并记录下该数值的索引，将负值取正；
2. 正常堆叠，但是鼠标悬浮上去的值，如果是正值的索引，预期目标值不变，实际完成是预期目标值+超出的数据，如果是负值的索引，两者的值互换位置；
3. 对的色块也要根据索引改变颜色。

说的有些费劲，还是上代码吧：

```
let data1 = [120, 200, 150, 80, 70, 110, 130]
let data2 = [12, 20, 15, -8, 7, -11, 13]
let indexes = []

data2.forEach((v,k)=>{
    if(v<0){
        indexes.push(k)
        data1[k] =  data1[k]+v
        data2[k] =  Math.abs(v)
    }
})

option = {
    xAxis: {
        type: 'category',
        data: ['shop1', 'shop2', 'shop3', 'shop4', 'shop5', 'shop5', 'shop6']
    },
    yAxis: {
        type: 'value',
        axisLabel: {
                formatter(value) {
                  return `${value} 万元`;
                }
              }
    },
     tooltip :{
            trigger: "axis",
            formatter(value) {
              if (indexes.indexOf(value[0].dataIndex) >= 0) {
                return `
              ${value[0].name}<br/>
              ${value[0].marker}${value[0].seriesName}: ${value[0].data +
                  value[1].data}<br />
              ${value[1].marker}${value[1].seriesName}: ${value[0].data}
              `;
              } else {
                return `
              ${value[0].name}<br/>
              ${value[0].marker}${value[0].seriesName}: ${value[0].data}<br />
              ${value[1].marker}${value[1].seriesName}: ${value[0].data +
                  value[1].data}
              `;
              }
            }
          },
    series: [{
        name:'预期销售额',
        stack:'one',
        data: data1,
        type: 'bar'
    },
    {
        name:'完成情况',
        stack:'one',
        data: data2,
        type: 'bar',
        itemStyle: {
                color: function(p) {
                  return  indexes.indexOf(p.dataIndex) >= 0 ? "red" : "purple";
                }}
    }]
};
```

预览效果如下：

![最终图表](https://i.loli.net/2019/06/29/5d1721c76600393971.png)

有点 low ，不过大体完成效果~~
