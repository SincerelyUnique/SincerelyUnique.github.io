---
title: 给el-select某个option添加label
date: 2021-04-15 15:10:05
tags:
- vue
categories:
---

```html
      <el-select ref="selectFeature"
                 v-model="feature"
                 id="selectFeatureBox"
                 clearable
                 @change = "changeFeature"
                 placeholder = "Please choose one new feature"
                 filterable
                 size='mini'
                 style="min-width: 600px;position: absolute;right:10px;top:5px;"
                 automatic-dropdown>
        <el-option v-for="item in featureOptions" :key="item.value" :label="item.label" :value="{value:item.value, label:item.label}">
          <span style="float: left">{{ item.label }}</span>
          <span v-if="['Feature1', 'Feature2'].indexOf(item.value) > -1" style="float: right; font-weight: 700;color: #12adec; font-size: 12px;">Flag</span>
        </el-option>
      </el-select>
```
