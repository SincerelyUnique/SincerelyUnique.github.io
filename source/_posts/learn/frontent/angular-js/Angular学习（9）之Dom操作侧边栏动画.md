---
title: Angular学习（9）之Dom操作侧边栏动画
date: 2022-08-25 14:34:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 涉及命令

```bash
PS D:\angular\angulardemo06> ng new angulardemo06    # 创建项目
PS D:\angular\angulardemo06> ng g component components/home    # 创建home组件
PS D:\angular\angulardemo06> ng g component components/news  # 创建新闻组件
PS D:\angular\angulardemo06> ng g component components/header   # 创建header头部组件
PS D:\angular\angulardemo06> ng g component components/transition   # 创建侧边栏组件
PS D:\angular\angulardemo06> ng serve --open   # 启动项目
```

# 全局样式(style.scss)

```scss
body{
    width: 100%;
    overflow-x: hidden;
}
```

# app.module.ts

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import { HomeComponent } from './components/home/home.component';
import { NewsComponent } from './components/news/news.component';
import { HeaderComponent } from './components/header/header.component';
import { TransitionComponent } from './components/transition/transition.component';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    NewsComponent,
    HeaderComponent,
    TransitionComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

# app.component.html

```html
<!-- 下面代码先注释掉，为了看transition侧边栏效果 -->
<!-- <app-home></app-home>

<br>
<hr>
<br>

<app-news></app-news>

<br>
<hr>
<br> -->

<app-transition></app-transition>
```

# header组件

## header.component.html

```html
<h1>我是一个头部组件</h1>
```

## header.component.scss

```scss
h1{
    text-align: center;
    color: #fff;
    background-color: #000;
}
```

## header.component.ts

```ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-header',
  templateUrl: './header.component.html',
  styleUrls: ['./header.component.scss']
})
export class HeaderComponent implements OnInit {

  constructor() { }

  ngOnInit(): void {
  }

  run(){
    console.log('我是header里面的run方法')
  }
}
```

# home组件

## home.component.html

```html
<h2>这是一个home组件--DOM操作演示</h2>

<div id="box">
    this is a box
</div>

<br>

<div id="box1" *ngIf="flag">
    this is a box1
</div>
```

## home.component.ts

```ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.scss']
})
export class HomeComponent implements OnInit {

  public flag:boolean = true

  constructor() { }

  ngOnInit(): void {
    // 声明周期函数，
    // 组件和指令初始化完成，并不是真正的dom加载完成
    // 我们也可以从这里获取dom节点
    let oBox:any = document.getElementById("box")
    console.log(oBox.innerHTML)
    oBox.style.color = 'red'

    // let oBox1:any = document.getElementById("box1")
    // 下面这一行报：TypeError: Cannot read properties of null (reading 'innerHTML')
    // 获取不到DOM节点
    // console.log(oBox1.innerHTML)
    // oBox1.style.color = 'blue'
  }

  ngAfterViewInit(): void{
    // 视图加载完成以后触发的方法，dom加载完成，建议把dom操作放在这个方法里面
    let oBox1:any = document.getElementById("box1")
    console.log(oBox1.innerHTML)
    oBox1.style.color = 'blue'
  }
}
```

# news组件

## news.component.html

```html
<app-header #header></app-header>

<div #mybox>
    我是一个DOM节点
</div>

<button (click)="getChildRun()">获取子组件的方法</button>
```

## news.component.ts

```ts
/**
 * ViewChild获取dom节点
 * 1. 模板中给dom起一个名字
 *    <div #mybox> 我是一个DOM节点 </div>
 * 2. 在业务逻辑里面引入
 *    import { Component, OnInit, ViewChild } from '@angular/core';
 * 3. 写在类里面的装饰器
 *    @ViewChild("mybox") myBox:any;
 * 4. ngAfterViewInit生命周期函数里面获取dom
 *    this.myBox.nativeElement
 */

import { Component, OnInit, ViewChild } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.scss']
})
export class NewsComponent implements OnInit {

  // 获取dom节点
  @ViewChild("mybox") myBox:any;

  // 获取一个组件
  @ViewChild("header") header:any;

  constructor() { 

  }

  ngOnInit(): void {
  }

  ngAfterViewInit(){
    console.log(this.myBox.nativeElement)
    this.myBox.nativeElement.style.width = "100px"
    this.myBox.nativeElement.style.height = "100px"
    this.myBox.nativeElement.style.background = 'red'
    console.log(this.myBox.nativeElement.innerHTML)

    // 调用子组件的方法
    this.header.run()
  }

  getChildRun(){
    // 调用子组件里面的方法
    this.header.run()
  }
}
```

# transition组件

## transition.component.html

```html
<div class="content">
    内容区域
    <button (click)="showAside()">弹出侧边栏</button>
    <button (click)="hideAside()">隐藏侧边栏</button>
</div>

<aside id="aside">
    只是一个侧边栏
</aside>
```

## transition.component.scss

```scss
#aside{
    width: 200px;
    height: 100%;
    position: absolute;
    right: 0px;
    top: 0px;
    background: #000;
    color: #fff;

    transform: translate(100%, 0);

    transition: all 2s;
}
```

## transition.component.ts

```ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-transition',
  templateUrl: './transition.component.html',
  styleUrls: ['./transition.component.scss']
})
export class TransitionComponent implements OnInit {

  constructor() { }

  ngOnInit(): void {
  }

  showAside(){
    // 直接用原生js获取dom节点
    var asideDom:any = document.getElementById("aside")
    asideDom.style.transform = "translate(0, 0)"
  }

  hideAside(){
    // 直接用原生js获取dom节点
    var asideDom:any = document.getElementById("aside")
    asideDom.style.transform = "translate(100%, 0)"
  }
}
```
