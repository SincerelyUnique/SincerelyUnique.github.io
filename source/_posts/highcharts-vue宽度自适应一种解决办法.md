---
title: highcharts-vue宽度自适应一种解决办法
date: 2021-01-18 16:47:50
tags:
- vue
categories:
---

```raw
前端框架：vue element ui
项目参考Git地址：https://github.com/PanJiaChen/vue-element-admin
 
 
说明：vue element ui集成highcharts-vue后，点击收拢或展开左侧菜单，
chart不会自动调整显示宽度。
 
 
修改位置：.\src\components\Charts\mixins\resize.js方法，vue components
以mixins混入方式引入resize.js作为可复用功能，实现对所有chart进行宽度
自适应。
 
 
修改resize.js：
import { debounce } from '@/utils'
 
export default {
  data() {
    return {
      $_sidebarElm: null
    }
  },
  mounted() {
    this.__resizeHandler = debounce(() => {
      if (this.chart) {
        this.chart.resize()
      }
      
      // 这里要求所有highcharts组件内部都要绑定一个ref属性，且名字叫chart，这点不太好，暂时没想到
      // 比较好的解决办法
      if (this.$refs.chart.chart) {
        // 方法一，指定高度和宽度，有animation渐入效果
        let chart = this.$refs.chart.chart
        let w = this.$refs.chart.$el.offsetWidth
        let h = this.$refs.chart.$el.offsetHeight
 
        chart.setSize(w, h);
        chart.hasUserSize = null;
        
        // 方法二，直接调用reflow()，但是不会触发animation（没有渐入效果）
        //this.$refs.chart.chart.reflow()
      }
    }, 100)
    window.addEventListener('resize', this.__resizeHandler)
 
    this.$_sidebarElm = document.getElementsByClassName('sidebar-container')[0]
    this.$_sidebarElm && this.$_sidebarElm.addEventListener('transitionend', this.$_sidebarResizeHandler)
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.__resizeHandler)
 
    this.$_sidebarElm && this.$_sidebarElm.removeEventListener('transitionend', this.$_sidebarResizeHandler)
  },
  methods: {
    $_sidebarResizeHandler(e) {
      if (e.propertyName === 'width') {
        debugger
        this.__resizeHandler()
      }
    }
  }
}
 
 
 
 
参考：
https://www.highcharts.com/forum/viewtopic.php?t=32608   （topic）
http://jsfiddle.net/ersz3b8a/      （highcharts animation演示）
https://github.com/highcharts/highcharts-vue  （highcharts-vue官方doc与source code）
https://my.oschina.net/u/2612473/blog/4355146  （以绑定ref属性方式对chart进行reflow）
https://juejin.cn/post/6844903908398071816  （vue mixins混入介绍）
https://panjiachen.github.io/vue-element-admin-site/guide/advanced/chart.html#demo （echart与highcarts一样本身无法主动自适应）
```
