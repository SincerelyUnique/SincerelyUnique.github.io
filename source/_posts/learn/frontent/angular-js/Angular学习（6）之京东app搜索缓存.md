---
title: Angular学习（6）之京东app搜索缓存
date: 2022-08-21 14:34:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 创建组件

```bash
PS D:\angular\angulardemo05> ng g component components/search
```

# app.module.ts引入form module

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { SearchComponent } from './components/search/search.component';

@NgModule({
  declarations: [
    AppComponent,
    SearchComponent
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
<app-search></app-search>
```

# search.component.html
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

# search.component.scss

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

# search.component.ts

```ts
import { Component, OnInit } from '@angular/core';

@Component({
  selector: 'app-search',
  templateUrl: './search.component.html',
  styleUrls: ['./search.component.scss']
})
export class SearchComponent implements OnInit {

  public keywords:string = ''

  public historyList:any[] = []

  constructor() { }

  ngOnInit(): void {
  }

  doSearch(){
    if (this.historyList.indexOf(this.keywords) == -1){
      this.historyList.push(this.keywords)
    }
    console.log(this.keywords)
    this.keywords = ''
  }

  deleteHistory(key:any){
    console.log(key)
    this.historyList.splice(key, 1)
  }
}
```
