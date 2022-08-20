---
title: Angular学习（3）之Angular组件以及组件中的模板合成
date: 2022-08-18 10:34:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Angular组件以及组件中的模板合成

下面在news组件中测试数据绑定以及普及一些数据结构、语法的基本使用。

```ts news.component.ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.scss']
})
export class NewsComponent implements OnInit {

  /**
   * 声明属性的几种方式
   * public 公有（default） 可以在这个类里面使用，也可以在类外面使用
   * protected  保护类型  他只有在当前类和他的子类里面才可以访问
   * private    私有      只有在当前类才可以访问这个属性
   */

  public title = "我是新闻组件";

  msg="我也是";

  userName:string="张三";

  public age:number=12;  //推荐方式

  public student:any="任何人";   //推荐方式

  public userInfo:any={
    userName: "张三",
    age: 20
  }

  public message:any;

  public content:string="<h4>我是一个html标签</h4>";

  items=['111', '222', '333']

  public list:number[]=[1, 2, 3, 4]

  public anyList:any[]=['1', 2, 'bbq', 4]

  public arrayList:Array<number>=[9, 8, 7, 6]

  public userList:Array<any>=[{
    name: "Jalen",
    age: 12
  },{
    name: "Jones",
    age:20
  }]

  public cars:any[]=[
    {
      cate: "宝马",
      list: [
        {
          title: "宝马x8",
          price: 300000
        },
        {
          title: "宝马x9",
          price: 300001
        },
        {
          title: "宝马x10",
          price: 300002
        }
      ]
    },
    {
      cate: "奔驰",
      list: [
        {
          title: "奔驰s8",
          price: 300003
        },
        {
          title: "奔驰s9",
          price: 300004
        },
        {
          title: "奔驰s10",
          price: 300005
        }
      ]
    }
  ]

  constructor() { 
    this.message = "这是给属性赋值";
    console.log(this.msg)
    this.msg = "我是改变后的msg的值"
  }

  ngOnInit(): void {
  }

}
```

```html news.component.html
<h1>Angular模板里绑定数据</h1>
<p>{{title}}</p>
<p>{{msg}}</p>
<p>{{userName}}</p>
I am <b>{{age}}</b> years old.
<p>{{student}}</p>
<p>{{userInfo.userName}} {{userInfo.age}}</p>
<p>{{message}}</p>

<h1>Angular模板里绑定属性</h1>
<div title="我是一个div">
    鼠标瞄上去看一下
</div>
<div [title]="student">
    张三
</div>

<h1>Angular模板里面绑定html</h1>
<div>
    {{content}}
</div>
<span [innerHTML]="content" class="red"></span>
<div [innerHTML]="content"></div>

<h1>Angular模板里允许做简单的运算</h1>
1 + 2 = {{1+2}}
4 - 3 = {{4-3}}


<h1>Angular里面的数据循环</h1>
<ol>
    <li *ngFor="let item of items">{{item}}</li>
</ol>
<br>
<ol>
    <li *ngFor="let item of anyList">{{item}}</li>
</ol>
<br>
<ul>
    <li *ngFor="let user of userList">{{user.name}} --- {{user.age}}</li>
</ul>
<br>
<ul>
    <li *ngFor="let car of cars">
        {{car.cate}}
        <ol>
            <li *ngFor="let series of car.list">{{series.title}} --- {{series.price}}</li>
        </ol>
    </li>
</ul>
```


