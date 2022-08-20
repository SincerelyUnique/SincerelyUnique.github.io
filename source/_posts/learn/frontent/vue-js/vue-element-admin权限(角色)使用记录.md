---
title: vue-element-admin权限(角色)使用记录
date: 2021-04-06 11:45:27
tags:
- vue
categories:
- [学习, 前端知识学习, VueJS]
---

```raw
vue element ui权限处理 - 前端登录时写入role信息
项目来自：https://github.com/PanJiaChen/vue-element-admin.git
具体可参考源码：src\views\permission\directive.vue
 
 
三种处理方式：
1.隐藏button  
可以通过使用vue element ui 中 v-permission 属性或 v-if 属性 或 v-show 属性实现。
但是隐藏button会导致用户看不到系统功能，对于系统推广不太好
（1）v-permission
     <el-button type="primary" v-permission="['admin', 'editor']">Confirm</el-button>
     import permission from '@/directive/permission/index.js'
     export default {
       name: '',
       directives: { permission },
     }
（2）v-if
     <span v-if="checkPermission(['admin', 'editor'])">
       <el-button type="primary">Confirm</el-button>
     </span>
     import checkPermission from '@/utils/permission'
     export default {
       name: '',
       methods: {
         checkPermission,
       }
     }
（3）v-show
     <el-button type="primary" v-show="checkPermission(['admin'])">Confirm</el-button>
     import checkPermission from '@/utils/permission'
     export default {
       name: '',
       methods: {
         checkPermission,
       }
     }
 
 
2.禁用button  
可以通过:disabled属性实现，但是禁用会导致用户认为该功能不对外开放或者说用户不明白是不是因为自身权限
造成了这个功能的disable
     <el-button type="primary" :disabled="checkPermission(['editor'])">Confirm</el-button>
     import checkPermission from '@/utils/permission'
     export default {
       name: '',
       methods: {
         checkPermission,
       }
     }
 
 
3.可点击，弹出没有权限提示
可以在utils\permission.js下添加一个方法，用户操作时候友好提示权限不足，如果想要使用的话，向团队申请，这个要比上面两种方式更友好些
     import Vue from 'vue'
     function isAccess() {
       if (checkPermission(['editor'])){
         Vue.prototype.$message({
           message: 'Permission denied, please contact xxx!',
           type: 'warning',
         })
         return true
       }
       return false
     }
 
     export { isAccess }
使用的时候
     import { isAccess } from '@/utils/permission'
操作时判断
     if (isAccess()) return false
 
 
 
 
 
 
备注：
1.另一种方式
如果只允许用户访问，不允许修改数据，可以在前端请求拦截器内获取角色信息并进一步拦截请求，
比如提示权限不足等，因为一般涉及后端数据更改都会走请求，即假如所有请求统一走axios的话，
可以大致做如下操作（未测试），这种方式需要维护url，感觉也不是太好
     import axios from 'axios'
     import Vue from 'vue'
     const accessUrlList = [
       '/api/country',
       '/api/city',
       '/api/province'
     ]
     axios.interceptors.request.use(
       config => {
         if (accessUrlList.indexOf(config.url) !== -1 && isAccess()) return config
         Vue.prototype.$message({
           message: 'Permission denied, please contact xxx!',
           type: 'warning',
         })
         return false
       },
       error => {
         return Promise.reject(error)
       }
     )
 
 
2.菜单权限控制
通过自带的roles控制即可，非指定role会隐藏菜单，如：
     {
       path: 'user',
       component: () => import('@/views/dashboard/user'),
       name: 'User',
       meta: { title: 'User', noCache: true, roles: ['Admin'] }
     }
```
