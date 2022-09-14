---
title: Angular学习（15）之get传值动态路由
date: 2022-09-14 05:03:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

> 继续使用上一节项目angulardemo11

# Get传值

## 1.跳转
```html
<ul>
    <li *ngFor="let item of list;let key=index">
        <!-- <a href="/newscontent?aid=123">&#123;&#123;key&#125;&#125; -- &#123;&#123;item&#125;&#125;</a> -->
        <a [routerLink]="[ '/newscontent' ]" [queryParams]="{aid:key}">&#123;&#123;key&#125;&#125; -- &#123;&#123;item&#125;&#125;</a>
    </li>
</ul>
```

## 2.接收
```ts
   import { ActivatedRoute } from '@angular/router';

   constructor(public route:ActivatedRoute) { }

   this.route.queryParams.subscribe(params => {
      console.log(params)
   })
```

# 动态路由

## 1.配置动态路由
```ts
  {
    path: 'newscontent/:aid',
    component: NewscontentComponent
  },
```

## 2.跳转
```html
<ul>
    <li *ngFor="let item of list;let key=index;">
        <a [routerLink]="[ '/newscontent', key ]">&#123;&#123;key&#125;&#125; -- &#123;&#123;item&#125;&#125;</a>
    </li>
</ul>
```

### 3.接收
```ts
    this.route.params.subscribe(params=>{
      console.log(params)
    })
```

# 跳转路由

## JS跳转路由

### 1.引入、声明模块
```ts
import { Router } from '@angular/router'
constructor(public router:Router) { }
```

### 2.跳转
```ts
this.router.navigate(['/home'])
this.router.navigate(['/newscontent/', '1243'])  // 带参数跳转
```

## Get传值跳转

### 1.引入、声明模块
```ts
import { Router, NavigationExtras } from '@angular/router'
constructor(public router:Router) { }
```

### 2.跳转
```ts
    // 跳转并进行传值
    let navigationExtras:NavigationExtras = {
      queryParams: {'aid': 123}
    }
    this.router.navigate(['/news'], navigationExtras)

    // 下面不使用NavigationExtras也可以跳转
    let queryParams = {
      queryParams: {'aid': 456}
    }
    this.router.navigate(['/news'], queryParams)
```

# 详细代码

## product组件

### product.component.html
```html
<p>product works!</p>
<br>
<br>
<a [routerLink]="['/productcontent/', '1234']">跳转到商品详情</a> 
<br>
<br>
<button (click)="goNewsContent()">JS跳转路由</button>
<br>
<br>
<button (click)="goHome()">JS跳转到首页</button>
<br>
<br>
<button (click)="goNews()">Get传值跳转路由</button>
```

### product.component.ts
```ts
import { Component, OnInit } from '@angular/core';

import { Router, NavigationExtras } from '@angular/router'

/**
 * JS 跳转路由
 * 1.动态路由
 * 
 * 1.引入、声明模块
 * import { Router } from '@angular/router'
 *   constructor(public router:Router) { }
 * 2.跳转
 * this.router.navigate(['/home'])
 * this.router.navigate(['/newscontent/', '1243'])
 * 
 * 2.get传值
 * 1.引入、声明模块
 * import { Router, NavigationExtras } from '@angular/router'
 * constructor(public router:Router) { }
 * 2.跳转
 * this.router.navigate(['/news'], {
      queryParams: {'aid': 456}
    })

    let navigationExtras:NavigationExtras = {
      queryParams: {'aid': 123}
    }
    this.router.navigate(['/news'], navigationExtras)
 */

@Component({
  selector: 'app-product',
  templateUrl: './product.component.html',
  styleUrls: ['./product.component.scss']
})
export class ProductComponent implements OnInit {

  constructor(public router:Router) { }

  ngOnInit(): void {
  }

  goNewsContent(){
    console.log('goNewsContent');
    // 路由跳转, 适合普通路由和动态路由
    this.router.navigate(['/newscontent/', '1243'])
  }

  goHome(){
    this.router.navigate(['/home'])
  }

  goNews(){
    // 跳转并进行传值
    let navigationExtras:NavigationExtras = {
      queryParams: {'aid': 123}
    }
    this.router.navigate(['/news'], navigationExtras)

    // 下面不使用NavigationExtras也可以跳转
    let queryParams = {
      queryParams: {'aid': 456}
    }
    this.router.navigate(['/news'], queryParams)
  }
}
```
