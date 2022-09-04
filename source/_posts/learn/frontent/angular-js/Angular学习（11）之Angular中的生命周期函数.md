---
title: Angular学习（11）之Angular中的生命周期函数
date: 2022-09-04 11:14:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Angular中的生命周期函数

官方文档：https://angular.cn/guide/lifecycle-hooks

生命周期函数通俗的讲就是组件创建、组件更新、组件销毁的时候会触发的一系列的方法。

当Angular使用构造函数新建一个组件或指令后，就会按下面的顺序在特定时刻调用这些生命周期钩子方法。

注意constructor不是生命周期函数，构造函数中使用了简单的值对局部变量进行初始化外，什么都不应该做。

|钩子|用途及时机|
|---|---|
|ngOnChanges()|当Angular（重新）设置数据绑定输入属性时响应。该方法接受当前和上一属性值的SimpleChanges对象；当被绑定的输入属性的值发生变化时调用，首次调用一定会发生在ngOnInit()之前。|
|***ngOnInit()***|在Angular第一次显示数据绑定和设置指令/组件的输入属性之后，初始化指令/组件。在第一轮ngOnChanges()完成之后调用，只调用一次。使用ngOnInit()有两个原因：1.在构造函数之后马上执行复杂的初始化逻辑；2.在Angular设置完输入属性之后，对该组件进行准|
|ngDoCheck()|检测，并在发生Angular无法或不愿意自己检测的变化时作出反应。|
|ngAfterContentInit()|当把内容投影进组件之后调用。第一次ngDoCheck()之后调用，只调用一次。|
|ngAfterContentChecked()|每次完成被投影组件内容的变更检测之后调用。ngAfterContentInit()和每次ngDoCheck()之后调用|
|***ngAfterViewInit()***|初始化完组件视图及其子视图之后调用。第一次ngAfterContentChecked()之后调用，只调用一次。|
|ngAfterViewChecked()|每次做完组件视图和子视图的变更检测之后调用。ngAfterViewInit()和每次ngAfterContentChecked()之后调用|
|***ngOnDestroy()***|当Angular每次销毁指令/组件之前调用并清扫。在这儿反订阅可观察对象和分离事件处理器，以防内存泄漏。在Angular销毁指令/组件之前调用|

上面copy的官网说明，实在让人看不懂。

# 创建项目

```bash
PS D:\angular> ng new angulardemo08
PS D:\angular> cd angulardemo08
PS D:\angular\angulardemo08> ng g component components/lifecycle
PS D:\angular\angulardemo08> ng serve --open
```

# 大致执行顺序

```ts
  constructor() { 
    console.log('00 构造函数执行了 -- 除了使用简单的值对局部变量进行初始化之外，什么都不应该做')
  }

  ngOnChanges(){
    console.log('01 ngOnChanges执行了 -- 当被绑定的输入属性的值发生变化时调用（父子组件传值的时候会触发）')
  }

  ngOnInit(): void {
    console.log('02 ngOnInit执行了 -- 请求数据一般放在这个里面')
  }

  ngDoCheck(){
    // 写一些自定义的操作
    console.log('03 ngDoCheck执行了 -- 检测，并在发生Angular无法或不愿意自己检测的变化时做出反应')
    if(this.userInfo !== this.oldUserInfo){
      console.log(`你从${this.oldUserInfo}改成${this.userInfo}`)
      this.oldUserInfo = this.userInfo
    }else{
      // this.counts = this.counts + 1
      console.log('数据没有变化')
    }
  }

  ngAfterContentInit(){
    console.log('04 ngAfterContentInit执行了 -- 当把内容投影进组件之后调用')
  }

  ngAfterContentChecked(){
    console.log('05 ngAfterContentChecked执行了 -- 每次完成被投影组件内容的变更检测之后调用')
  }

  ngAfterViewInit(){
    console.log('06 ngAfterViewInit执行了 -- 初始化完组件视图及其子视图之后调用（dom操作放在这个里面）')
  }

  ngAfterViewChecked(){
    console.log('07 ngAfterViewChecked执行了 -- 每次做完组件视图和子视图的变更检测之后调用')
  }

  ngOnDestroy(){
    console.log('08 ngOnDestroy执行了 --')
  }
  ```

# 详细代码

## app根组件

### app.module.js

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import { LifecycleComponent } from './components/lifecycle/lifecycle.component';

// 双向数据绑定必须引入
import { FormsModule } from '@angular/forms'

@NgModule({
  declarations: [
    AppComponent,
    LifecycleComponent
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### app.component.ts

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss']
})
export class AppComponent {
  title = 'angulardemo08';
  public flag:boolean = true;

  changeTitle(){
    this.title = "改变后的title"
  }

  changeFlag(){
    this.flag = !this.flag
  }
}
```

### app.component.html

```html
<app-lifecycle [title]="title" *ngIf="flag"></app-lifecycle>
<br>
<hr>
<p>这是父组件</p>
<button (click)="changeTitle()">改变父组件的title</button>
<br>
<br>
<button (click)="changeFlag()">挂载以及卸载组件</button>
```

## lifecycle组件

### lifecycle.component.html

```html
<h1>&#123;&#123;msg&#125;&#125;</h1>
<br>
<button (click)="changeMsg()">改变msg的值</button>
<input type="text" [(ngModel)]="userInfo"/>
```

### lifecycle.component.ts

```ts
import { Component, OnInit, Input } from '@angular/core';

@Component({
  selector: 'app-lifecycle',
  templateUrl: './lifecycle.component.html',
  styleUrls: ['./lifecycle.component.scss']
})
// 下面实现了OnInit接口，接口是可选的
export class LifecycleComponent implements OnInit {

  public msg:string = "我是一个生命周期演示"

  public userInfo:string=''
  public oldUserInfo:string=''

  public counts:number=0

  @Input('title') title:any;

  constructor() { 
    console.log('00 构造函数执行了 -- 除了使用简单的值对局部变量进行初始化之外，什么都不应该做')
  }

  ngOnChanges(){
    console.log('01 ngOnChanges执行了 -- 当被绑定的输入属性的值发生变化时调用（父子组件传值的时候会触发）')
  }

  ngOnInit(): void {
    console.log('02 ngOnInit执行了 -- 请求数据一般放在这个里面')
  }

  ngDoCheck(){
    // 写一些自定义的操作
    console.log('03 ngDoCheck执行了 -- 检测，并在发生Angular无法或不愿意自己检测的变化时做出反应')
    if(this.userInfo !== this.oldUserInfo){
      console.log(`你从${this.oldUserInfo}改成${this.userInfo}`)
      this.oldUserInfo = this.userInfo
    }else{
      // this.counts = this.counts + 1
      console.log('数据没有变化')
    }
  }

  ngAfterContentInit(){
    console.log('04 ngAfterContentInit执行了 -- 当把内容投影进组件之后调用')
  }

  ngAfterContentChecked(){
    console.log('05 ngAfterContentChecked执行了 -- 每次完成被投影组件内容的变更检测之后调用')
  }

  ngAfterViewInit(){
    console.log('06 ngAfterViewInit执行了 -- 初始化完组件视图及其子视图之后调用（dom操作放在这个里面）')
  }

  ngAfterViewChecked(){
    console.log('07 ngAfterViewChecked执行了 -- 每次做完组件视图和子视图的变更检测之后调用')
  }

  ngOnDestroy(){
    console.log('08 ngOnDestroy执行了 --')
  }

  // 自定义方法
  changeMsg(){
    this.msg = "数据改变了"
  }
}
```
