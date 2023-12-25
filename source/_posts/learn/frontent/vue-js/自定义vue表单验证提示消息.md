---
title: 自定义vue表单验证提示消息
date: 2023-09-26 15:03:36
tags:
- vue
categories:
- [学习, 前端知识学习, VueJS]
---

# 需求

用户在表单中某个input框输入数据后，点击查询，这时访问后端API，如果后端API没有返回数据，此时需要在input框下面显示红色的提示文字

# 通常表单验证写法

```vue
<template>
  <div>
    <el-form
        :rules="formRules"
        ref="form">
      <el-form-item prop="xxx">
        <el-input
            v-model.trim="value"
            clearable>
        </el-input>
      </el-form-item>
    </el-form>
  </div>
</template>
<script>
  export default {
    methods: {
      test() {
        this.$refs['form'].fields[1].validateState = 'success';
        this.$refs['form'].fields[1].validateMessage = '';
      }
    }
  }
</script>
```
