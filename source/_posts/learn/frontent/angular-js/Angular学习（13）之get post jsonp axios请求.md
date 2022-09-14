---
title: Angular学习（13）之get post jsonp axios请求
date: 2022-09-14 03:54:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 准备

```bash
ng new angulardemo10
ng g component components/news
ng g service services/httpservice
ng serve --open
```

# GET/POST请求

1. 根组件引入HttpClientModule
```ts
import { HttpClientModule } from '@angular/common/http';
imports: [ HttpClientModule ]
```

2. 新闻组件引入HttpClient, HttpHeaders
```ts
import { HttpClient, HttpHeaders } from '@angular/common/http';

  public list:any[] = [];

  constructor(public http:HttpClient) { }

  getData(){
    console.log('执行get请求')
    this.http.get('http://a.itying.com/api/productlist').subscribe((res:any)=>{
      // RxJS
      console.log(res)
      this.list = res.result;
    })
  }

  postData(){
    console.log('执行post请求')
    const httpOptions = {
      headers: new HttpHeaders({
        'Content-Type': 'application/json'
      })
    };
    let api = 'http://127.0.0.1:8079/data'
    this.http.post(api, {
      'name': 'user'
    }, httpOptions).subscribe((res)=>{
      console.log(res);
    })
  }
```

# jsonp请求

1. 根组件引入
```ts
import { HttpClientJsonpModule } from '@angular/common/http';
imports: [ HttpClientJsonpModule ]
```

2. 新闻组件使用jsonp
```ts
  getJsonpData(){
    console.log('通过jsonp获取服务器端数据，跨域的一种解决方式')

    /**
     * jsonp请求，服务器必须支持jsonp，验证服务器支持jsonp
     * http://a.itying.com/api/productlist?callback=xxx
     * http://a.itying.com/api/productlist?cb=xxx
     */
    let api = 'http://a.itying.com/api/productlist'
    this.http.jsonp(api, 'callback').subscribe((res)=>{
      console.log(res);
    })
  }
```

# axios请求

1. 创建httpservice服务并在根组件引入该服务
```ts
import { HttpserviceService } from './services/httpservice.service';
providers: [HttpserviceService],
```

2. 服务里封装axios请求
```ts
import axios from 'axios'

  axiosGet(api:any){
    return new Promise((resolve, reject)=>{
      axios.get(api).then(function (response) {
        resolve(response)
      })
    })
  }
```

3. 新闻组件里引入该服务并使用
```ts
import { HttpserviceService } from 'src/app/services/httpservice.service';

  constructor(public httpService:HttpserviceService) { }

  getAxiosData(){
    console.log('通过第三方模块获取服务器的数据')
    let api = 'http://a.itying.com/api/productlist'
    this.httpService.axiosGet(api).then((res)=>{
      console.log(res);
    })
  }
```

# 详细代码

## 根组件app.module.ts

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { HttpClientModule, HttpClientJsonpModule } from '@angular/common/http';

import { AppComponent } from './app.component';
import { NewsComponent } from './components/news/news.component';

import { HttpserviceService } from './services/httpservice.service';

@NgModule({
  declarations: [
    AppComponent,
    NewsComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule,
    HttpClientJsonpModule
  ],
  providers: [HttpserviceService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

## app.component.html

```html
<app-news></app-news>
```

## news组件

### news.component.html
```html
<button (click)="getData()">Get请求数据</button>
<ul>
    <li *ngFor="let item of list">&#123;&#123;item.title&#125;&#125;</li>
</ul>
<button (click)="postData()">Post提交数据</button>
<button (click)="getJsonpData()">通过jsonp获取服务器端数据，跨域的一种解决方式</button>
<button (click)="getAxiosData()">通过第三方模块获取服务器的数据</button>
```

### news.component.ts
```ts
import { Component, OnInit } from '@angular/core';

import { HttpClient, HttpHeaders } from '@angular/common/http';

// 使用服务里axios获取数据
import { HttpserviceService } from 'src/app/services/httpservice.service';

@Component({
  selector: 'app-news',
  templateUrl: './news.component.html',
  styleUrls: ['./news.component.scss']
})
export class NewsComponent implements OnInit {

  public list:any[] = [];

  constructor(public http:HttpClient, public httpService:HttpserviceService) { }

  ngOnInit(): void {
  }

  getData(){
    console.log('执行get请求')
    this.http.get('http://a.itying.com/api/productlist').subscribe((res:any)=>{
      // RxJS
      console.log(res)
      this.list = res.result;
    })
  }

  postData(){
    console.log('执行post请求')
    const httpOptions = {
      headers: new HttpHeaders({
        'Content-Type': 'application/json'
      })
    };
    let api = 'http://127.0.0.1:8079/data'
    this.http.post(api, {
      'name': 'user'
    }, httpOptions).subscribe((res)=>{
      console.log(res);
    })
  }

  getJsonpData(){
    console.log('通过jsonp获取服务器端数据，跨域的一种解决方式')

    /**
     * jsonp请求，服务器必须支持jsonp，验证服务器支持jsonp
     * http://a.itying.com/api/productlist?callback=xxx
     * http://a.itying.com/api/productlist?cb=xxx
     */
    let api = 'http://a.itying.com/api/productlist'
    this.http.jsonp(api, 'callback').subscribe((res)=>{
      console.log(res);
    })
  }

  getAxiosData(){
    console.log('通过第三方模块获取服务器的数据')
    let api = 'http://a.itying.com/api/productlist'
    this.httpService.axiosGet(api).then((res)=>{
      console.log(res);
    })
  }
}
```

## httpservice服务
### httpservice.service.ts
```ts
import { Injectable } from '@angular/core';

import axios from 'axios'

@Injectable({
  providedIn: 'root'
})
export class HttpserviceService {

  constructor() { }

  axiosGet(api:any){
    return new Promise((resolve, reject)=>{
      axios.get(api).then(function (response) {
        resolve(response)
      })
    })
  }
}
```
