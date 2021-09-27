---
title: v-loading修改z-index
date: 2021-01-09 01:02:16
tags:
- vue
categories:
---

```html
<template>
  <div :id="id"
       :class="className"
       :style="{height:height,width:width}"
       v-loading="loading"
       element-loading-text="Loading"
       element-loading-spinner="el-icon-loading"/>
</template>
<script>
export default {
    created() {
      this.getChartData()
    },
    methods: {
      getChartData() {
        this.loading = true
        postRequest({
          url: '/api/charts',
          method: 'post',
          data: {
            fromDate: this.timeRange[0],
            toDate: this.timeRange[1]
          }
        }).then(res => {
          this.loading = false
          this.lineData = res.data
          this.initChart()
        }).catch(error=> {
          this.loading = false
          this.$message({
            type: 'error',
            message: 'Request error, try again!'
          });
        })
      }
 
      ... ...
    }  
}
```

上面是一个子组件，在父组件初始化时，即刚进页面时，加载该子组件，该子组件会先执行create(),

在create()方法内有调用ajax请求到后端，请求过程中显示loading，发现此时v-loading遮罩默认显示在所有div的最上层，

且此时滚动鼠标滑轮，发现遮罩层会向上挪动从而覆盖上部菜单栏，

F12后发现v-loading组件样式属性的z-index为2000，太大，调整为8时正好，即需要修改z-index属性，

尝试从css里写样式覆盖，发现不行，暂时修改如下，但是这样写每个组件都要加mounted，比较啰嗦，后期有时间在找找其他方法

```javascript
    mounted() {
      this.$nextTick(() => {
        let loadingNode = document.getElementsByClassName('el-loading-mask')
        for (let i = 0; i < loadingNode.length; i++) {
          loadingNode[i].style.zIndex = 8
        }
      })
    }
```

说明，一定要在mounted过后并且在nextTick内部去做属性修改，因为只有此时page该节点dom才会渲染出来，否则操作不到该dom导致修改无效
