---
title: Angular学习（8）之服务以及实现数据持久化
date: 2022-08-25 14:34:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 相关命令

```bash
PS D:\angular\angulardemo05> ng --help    # 获取帮助
PS D:\angular\angulardemo05> ng g --help     # 获取帮助
PS D:\angular\angulardemo05> ng g service services/storage    # 创建服务
PS D:\angular\angulardemo05> ng serve --open     # 启动项目
```

# app.module.ts

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { SearchComponent } from './components/search/search.component';
import { TodolistComponent } from './components/todolist/todolist.component';

// 引入并且配置服务
import { StorageService } from './services/storage.service'

@NgModule({
  declarations: [
    AppComponent,
    SearchComponent,
    TodolistComponent
  ],
  imports: [
    BrowserModule,
    FormsModule
  ],
  providers: [StorageService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

# app.component.html

```html
<app-search></app-search>
<app-todolist></app-todolist>
```

# 搜索缓存

## search.component.html

```html
<div class="search">
    <input type="text" [(ngModel)]="keywords"/> &nbsp;<button (click)="doSearch()">搜索</button>
    <hr>
    <ul>
        <li *ngFor="let item of historyList;let key=index;">
            &#123;&#123;item&#125;&#125;
            <button (click)="deleteHistory(key)">x</button>
        </li>
    </ul>
</div>
```

## search.component.scss

```scss
.search{
    width: 400px;
    margin: 20px auto;

    input{
        margin-bottom: 20px;
        width: 300px;
        height: 32px;
    }

    button{
        height: 32px;
        width: 80px;
    }
}
```

## search.component.ts

```ts
/**
 * 服务简要说明
 * 1. 创建服务：ng g service services/storage
 * 2. app.module.ts里面引入创建的服务
 *    import { StorageService } from './services/storage.service'
 * 3. NgModule里面的providers里面依赖注入服务
 *    providers: [StorageService],
 * 4. 在用到的组件里面引入服务
 *    import { StorageService } from '../../services/storage.service'
 * 5. 初始化时候在构造函数里面通过参数传递这个服务，然后通过this调用这个服务
 *    constructor(public storage:StorageService) { let s = this.storage.get() console.log(s) }
 * 注意：组件里可以调用服务
 *      服务里不能调用组件
 *      组件里不能调用组件
 *      服务里可以调用服务
 * 
 *      组件里可以传值（父子组件）
 */

import { Component, OnInit } from '@angular/core';
// 引入服务
import { StorageService } from '../../services/storage.service'

// 不推荐这种使用方式
// var storage = new StorageService()
// console.log(storage)
// console.log(storage.get())

@Component({
  selector: 'app-search',
  templateUrl: './search.component.html',
  styleUrls: ['./search.component.scss']
})
export class SearchComponent implements OnInit {

  public keywords:string = ''

  public historyList:any[] = []

  // 推荐使用构造方式注入
  constructor(public storage:StorageService) { 
    // let s = this.storage.get()
    let s = this.storage.get('111')
    console.log(s)
  }

  ngOnInit(): void {
    // 刷新页面或刷新组件会触发该生命周期函数
    console.log("触发刷新init方法")
    var searchList:any = this.storage.get('searchList')
    if(searchList){
      this.historyList = searchList
    }
  }

  doSearch(){
    if (this.historyList.indexOf(this.keywords) == -1){
      this.historyList.push(this.keywords)
      this.storage.set('searchList', this.historyList)
    }
    console.log(this.keywords)
    this.keywords = ''
  }

  deleteHistory(key:any){
    console.log(key)
    this.historyList.splice(key, 1)
    this.storage.set('searchList', this.historyList)
  }
}
```

# 待办事项

## todolist.component.html

```html
<h2>To do list</h2>
<div class="todolist">
    <input type="text" class="form_input" [(ngModel)]="keywords" (keyup)="doAdd($event)"/>
    <hr>

    <h3>待办事项</h3>
    <ul>
        <li *ngFor="let item of todoList;let key=index;" [hidden]="item.status==1">
            <input type="checkbox" [(ngModel)]="item.status" (change)="checkboxChange()"/>&#123;&#123;item.title&#125;&#125; ---- <button (click)="deleteData(key)">x</button>
            &#123;&#123;item&#125;&#125;
            
        </li>
    </ul>

    <h3>已经完成</h3>
    <ul>
        <li *ngFor="let item of todoList;let key=index;" [hidden]="item.status==0">
            <input type="checkbox" [(ngModel)]="item.status" (change)="checkboxChange()"/>&#123;&#123;item.title&#125;&#125; ---- <button (click)="deleteData(key)">x</button>
            &#123;&#123;item&#125;&#125;
            
        </li>
    </ul>
</div>
```

## todolist.component.scss

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

## todolist.component.ts

```ts
import { Component, OnInit } from '@angular/core';

import { StorageService } from '../../services/storage.service'

@Component({
  selector: 'app-todolist',
  templateUrl: './todolist.component.html',
  styleUrls: ['./todolist.component.scss']
})
export class TodolistComponent implements OnInit {

  public keywords:string = ''

  public todoList:any[] = []

  constructor(public storage:StorageService) { 
    // console.log(this.storyService.get())
    console.log(this.storage.get('111'))
  }

  ngOnInit(): void {
    var searchList:any = this.storage.get('todoList')
    if(searchList){
      this.todoList = searchList
    }
  }

  doAdd(e:any){
    if(!this.todoListHasKeywords(this.todoList, this.keywords)){
      if(e.keyCode == 13){   //keyCode=13是回车
        this.todoList.push({
          title: this.keywords,
          status: 0     // 0：待办事项，1：已完成
        })
        this.keywords = ''

        this.storage.set('todoList', this.todoList)   // 用到this，一定要注意this的指向
      }
    }else{
      alert(this.keywords + " 已经存在了!")
      this.keywords = ''
    }
  }

  deleteData(key:any){
    this.todoList.splice(key, 1)
    this.storage.set('todoList', this.todoList)
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

  checkboxChange(){
    console.log('Change事件触发了')
    this.storage.set('todoList', this.todoList)
  }
}
```

# 创建storage服务

通过storage服务调用localStorage持久化上面的搜索缓存和待办事项数据

## storage service

```ts
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class StorageService {

  constructor() { }

  set(key:string, value:any){
    localStorage.setItem(key, JSON.stringify(value))
  }

  get(key:string){
    //return 'this is a service.'
    // return JSON.parse(localStorage.getItem(key) || '0')
    return JSON.parse(localStorage.getItem(key) as string)
  }
  
  remove(key:string){
    localStorage.removeItem(key)
  }
}
```


