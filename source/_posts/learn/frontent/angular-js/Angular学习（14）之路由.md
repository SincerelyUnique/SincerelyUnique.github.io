---
title: Angular学习（14）之路由
date: 2022-09-14 03:58:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 路由的作用
路由就是根据不同url地址，动态地让根组件挂载其他组件来实现一个单页面应用。

# 路由的使用
1. 创建一个带路由的项目，第一个选择选y
```bash
ng new angulardemo11
```
2. 创建需要的组件
```bash
ng g component components/home
ng g component components/news
ng g component components/product
```
3. 跟模块引入组件并注入
4. 路由模块引入组件并在routes里面配置path和component
5. 根页面引入<router-outlet>标签
6. 使用routerLink跳转，可以通过routerLinkActive设置active样式

# 完整代码

## app.module.ts
```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { NewsComponent } from './components/news/news.component';
import { HomeComponent } from './components/home/home.component';
import { ProductComponent } from './components/product/product.component';

@NgModule({
  declarations: [
    AppComponent,
    NewsComponent,
    HomeComponent,
    ProductComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## app.component.scss
```scsss
.header{
    height: 44px;
    line-height: 44px;

    background-color: #000;
    color: #fff;

    a{
        color: #fff;
        padding: 10px 40px;
    }

    .active{
        color: red;
    }
}
```

## app.component.html
```html
<header class="header">
  <!-- <a [routerLink]="[ '/home' ]">首页</a>
  <a routerLink="/news">新闻</a>
  <a [routerLink]="[ '/product' ]">商品</a> -->

  <a [routerLink]="[ '/home' ]" routerLinkActive="active">首页</a>
  <a routerLink="/news" routerLinkActive="active">新闻</a>
  <a [routerLink]="[ '/product' ]" routerLinkActive="active">商品</a>
</header>
<router-outlet></router-outlet>
```

## app-routing.module.ts
```ts
// 配置路由的模块

import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { NewsComponent } from './components/news/news.component';
import { HomeComponent } from './components/home/home.component';
import { ProductComponent } from './components/product/product.component';

const routes: Routes = [
  {
    path: 'home',
    component: HomeComponent
  },
  {
    path: 'news',
    component: NewsComponent
  },
  {
    path: 'product',
    component: ProductComponent
  },
  {
    path: '**',  // 任意路由，默认跳转到home页面
    redirectTo: 'home'
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```



