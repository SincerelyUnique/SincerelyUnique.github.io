---
title: Angular学习（16）之路由的嵌套和父子路由
date: 2022-09-15 07:13:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 准备
```bash
PS D:\angular> ng new angulardemo12
PS D:\angular> cd angulardemo12
PS D:\angular\angulardemo12> ng g component components/home  
PS D:\angular\angulardemo12> ng g component components/product 
PS D:\angular\angulardemo12> ng g component components/home/welcome
PS D:\angular\angulardemo12> ng g component components/home/setting 
PS D:\angular\angulardemo12> ng g component components/product/pcate
PS D:\angular\angulardemo12> ng g component components/product/plist
PS D:\angular\angulardemo12> ng g component components/news
PS D:\angular\angulardemo12> ng serve --open
```

# 代码
## 根组件及全局配置
### app.module.ts
```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { HomeComponent } from './components/home/home.component';
import { ProductComponent } from './components/product/product.component';
import { WelcomeComponent } from './components/home/welcome/welcome.component';
import { SettingComponent } from './components/home/setting/setting.component';
import { PcateComponent } from './components/product/pcate/pcate.component';
import { PlistComponent } from './components/product/plist/plist.component';
import { NewsComponent } from './components/news/news.component';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    ProductComponent,
    WelcomeComponent,
    SettingComponent,
    PcateComponent,
    PlistComponent,
    NewsComponent
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

### app.component.scss
```scss
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

### app.component.html
```html
<header class="header">
  <a [routerLink]="[ '/home' ]" routerLinkActive="active">首页</a>
  <a [routerLink]="[ '/product' ]" routerLinkActive="active">商品</a>
  <a [routerLink]="[ '/news' ]" routerLinkActive="active">新闻</a>
</header>
<router-outlet></router-outlet>
```

### app-routing.module.ts
```ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { HomeComponent } from './components/home/home.component';
import { ProductComponent } from './components/product/product.component';

import { WelcomeComponent } from './components/home/welcome/welcome.component';
import { SettingComponent } from './components/home/setting/setting.component';
import { PcateComponent } from './components/product/pcate/pcate.component';
import { PlistComponent } from './components/product/plist/plist.component';

import { NewsComponent } from './components/news/news.component';

const routes: Routes = [
  {
    path: 'home', 
    component: HomeComponent,
    children: [
      {
        path: 'welcome',
        component: WelcomeComponent
      },
      {
        path: 'setting',
        component: SettingComponent
      },
      {
        path: '**',
        redirectTo: 'welcome'
      }
    ]
  },
  {
    path: 'product', 
    component: ProductComponent,
    children: [
      {
        path: 'plist',
        component: PlistComponent
      },
      {
        path: 'pcate',
        component: PcateComponent
      },
      {
        path: '**',
        redirectTo: 'plist'
      }
    ]
  },
  {
    path: 'news', 
    component: NewsComponent
  },
  {
    path: '**', 
    redirectTo: 'home'
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

### style.scss
```scss
/* You can add global styles to this file, and also import other style files */
.content{
    width: 100%;
    height: 500px;
    display: flex;

    .left{
        width: 200px;
        border-left: 1px solid #eee;
        height: 500px;
    }

    .right{
        flex: 1px;
    }
}

.active{
    color: red;
}
```

## home组件
### home.component.html
```html
<div class="content">
    <div class="left">
        <!-- <a href="#">欢迎首页</a> -->
        <a [routerLink]="[ '/home/welcome' ]" routerLinkActive="active">欢迎首页</a>
        <br>
        <br>
        <!-- <a href="#">系统设置</a> -->
        <a [routerLink]="[ '/home/setting' ]" routerLinkActive="active">系统设置</a>
    </div>
    <div class="right">
        <router-outlet></router-outlet>
    </div>
</div>
```

## product组件
### product.component.html
```html
<div class="content">
    <div class="left">
        <!-- <a href="#">商品分类列表</a> -->
        <a [routerLink]="[ '/product/pcate' ]" routerLinkActive="active">商品分类</a>
        <br>
        <br>
        <!-- <a href="#">商品列表</a> -->
        <a [routerLink]="[ '/product/plist' ]" routerLinkActive="active">商品列表</a>
    </div>
    <div class="right">
        <router-outlet></router-outlet>
    </div>
</div>
```

