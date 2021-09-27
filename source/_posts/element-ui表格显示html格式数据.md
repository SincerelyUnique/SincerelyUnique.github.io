---
title: element-ui表格显示html格式
date: 2020-07-08 13:28:36
tags:
- vue
categories:
---

后端传的字段enablementplan里带有html标签，想在vue页面的table里看到html 标签显示效果，比如颜色之类的 

```html
<el-table-column type="String" prop="enablementplan" label="EnablementPlan" sortable width="300">
  <template slot-scope="scope">
    <p v-html='scope.row.enablementplan'></p>
  </template>
</el-table-column>
```

转自：https://blog.csdn.net/weixin_30708329/article/details/101145081
