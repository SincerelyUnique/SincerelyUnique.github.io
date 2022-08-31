---
title: Angular学习（7）之待办事项
date: 2022-08-21 15:34:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 创建组件

```bash
PS D:\angular\angulardemo05> ng g component components/todolist
```

# app.module.ts引入form module

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { TodolistComponent } from './components/search/todolist.component';

@NgModule({
  declarations: [
    AppComponent,
    TodolistComponent
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

# app.component.html引入组件标签

```html
<app-todolist></app-todolist>
```

# search.component.html
```html
<h2>To do list</h2>
<div class="todolist">
    <input type="text" class="form_input" [(ngModel)]="keywords" (keyup)="doAdd($event)"/>
    <hr>

    <h3>待办事项</h3>
    <ul>
        <li *ngFor="let item of todoList;let key=index;" [hidden]="item.status==1">
            <input type="checkbox" [(ngModel)]="item.status"/>&#123;&#123;item.title&#125;&#125; ---- <button (click)="deleteData(key)">x</button>
            &#123;&#123;item&#125;&#125;
            
        </li>
    </ul>

    <h3>已经完成</h3>
    <ul>
        <li *ngFor="let item of todoList;let key=index;" [hidden]="item.status==0">
            <input type="checkbox" [(ngModel)]="item.status"/>&#123;&#123;item.title&#125;&#125; ---- <button (click)="deleteData(key)">x</button>
            &#123;&#123;item&#125;&#125;
            
        </li>
    </ul>
</div>
```

# search.component.scss

```scss
h2{
    text-align: center;
}
.todolist{
    width: 400px;
    margin: 20px auto;

    .form_input{
        margin-bottom: 20px;
        width: 300px;
        height: 32px;
    }

    li{
        line-height: 60px;
    }
}
```

# search.component.ts

```ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-todolist',
  templateUrl: './todolist.component.html',
  styleUrls: ['./todolist.component.scss']
})
export class TodolistComponent implements OnInit {

  public keywords:string = ''

  public todoList:any[] = []

  constructor() { }

  ngOnInit(): void {
  }

  doAdd(e:any){
    if(!this.todoListHasKeywords(this.todoList, this.keywords)){
      if(e.keyCode == 13){   //keyCode=13是回车
        this.todoList.push({
          title: this.keywords,
          status: 0     // 0：待办事项，1：已完成
        })
        this.keywords = ''
      }
    }else{
      alert(this.keywords + " 已经存在了!")
      this.keywords = ''
    }
  }

  deleteData(key:any){
    this.todoList.splice(key, 1)
  }

  todoListHasKeywords(todoList:any[], keywords:string): any{
    // todoList.forEach(e=>{
    //   if(e.title == keywords){
    //     return true
    //   }
    //   return false
    // })
    for (let i=0;i<todoList.length;i++){
      if(todoList[i].title == keywords){
        return true
      }
    }
    return false
  }
}
```
