---
title: Angular学习（12）之Rxjs快速入门
date: 2022-09-04 23:29:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Rxjs介绍

[参考手册](https://www.npmjs.com/package/rxjs)

[中文手册](https://cn.rx.js.org/)

RxJS是ReactiveX编程理念的javascript版本。reactiveX来自微软，它是一种针对异步数据流的编程。简单来说，它将一切数据，包括HTTP请求，DOM事件或者普通数据等包装成流的形式，然后用强大丰富的操作符对流进行处理，使你能以同步编程的方式处理异步数据，并组合不同的操作符来轻松优雅的实现你所需要的功能。

RxJS相当于es6的Promise，但是比Promise更强大，比如RxJS中可以中途撤回、可以发射多个值、并且提供了多种工具函数等。Angular引入RxJS为了就是让异步可控、更简单。RxJS里面提供了很多模块。这里我们主要了解下最常用的Observable和fromEvent。

目前常见的异步编程的几种方法：
- 回调函数
- 事件监听/发布订阅
- Promise
- RxJS

# 创建项目

```bash
D:\angular>ng new angulardemo09
D:\angular>cd angulardemo09
D:\angular\angulardemo09>ng g component components/home
D:\angular\angulardemo09>ng g service services/request
D:\angular\angulardemo09>ng serve --open
```

# 简单演示

## services服务提供数据

```ts
  // 同步方法，测试获取同步数据
  getData(){
    return 'this is service data'
  }

  // 异步方法，测试直接获取异步数据
  getAsyncData(){
    setTimeout(() => {
      let name = '张三-async';
      return name;
    }, 1000)
  }

  // 通过回调函数模拟异步，获取异步数据
  getCallbackData(cb:any){
    setTimeout(() => {
      let name = '张三-callback';
      cb(name);
      return name;
    }, 1000)
  }

  // 通过Promise获取异步数据
  getPromiseData(){
    return new Promise((resolve, reject)=>{
      setTimeout(() => {
        let name = '张三-Promise';
        resolve(name);
      }, 1000)
    })
  }

  // 通过rxJS来处理异步数据
  getRxJSData(){
    return new Observable((observer)=>{
      setTimeout(() => {
        let name = '张三-rxJS';
        observer.next(name);
        //observer.error(name);
      }, 3000)
    })
  }
```

## 组件里面调用服务获取数据

```ts
  ngOnInit(): void {
    // 1.同步方法
    let data = this.request.getData();
    console.log(data);

    // 2.直接获取异步数据, 会返回undefined，说明异步数据无法在外部直接获取
    let callbackData = this.request.getAsyncData();
    console.log(callbackData);

    // 3.通过回调函数获取异步数据
    this.request.getCallbackData((data:any)=>{
      console.log(data)
    });

    // 4.通过Promise获取异步数据
    let promiseData = this.request.getPromiseData();
    promiseData.then((data)=>{
      console.log(data);
    })

    // 5.通过RxJS获取异步方法里面的数据
    let rxJSData = this.request.getRxJSData();
    rxJSData.subscribe((data)=>{
      console.log(data);
    })
  }
```

# RxJS取消订阅

Promise创建之后无法撤回，但是RxJS可以。同上面services里的例子：

## 服务

```ts
// 通过rxJS来提供异步数据
getRxJSData(){
  return new Observable((observer)=>{
    setTimeout(() => {
      let name = '张三-rxJS';
      observer.next(name);
      //observer.error(name);
    }, 3000)
  })
}
```

## 组件

```ts
// 过1s以后撤回刚才的操作
let stream = this.request.getRxJSData();
let d = stream.subscribe((data)=>{
  console.log(data);
})
setTimeout(() => {
  d.unsubscribe();  // 取消订阅
}, 1000)
```

# RxJS订阅后多次执行

比如我们有个异步方法，要求每个2s返回数据（定时器），如果使用Promise，那么这个方法只会执行1次，此时就可以使用RxJS。每隔2s去拿数据。

## 测试Promise是否可以多次执行

### 服务提供数据

```ts
// 测试promise多次执行
getPromiseIntervalData(){
  return new Promise((resolve)=>{
    setInterval(() => {
      let name = '张三-Promise-interval';
      resolve(name);
    }, 1000)
  })
}
```

### 组件调用服务

```ts
// 测试promise多次执行（发现只执行了一次，说明Promise没有多次执行这个能力）
let intervalData = this.request.getPromiseIntervalData();
intervalData.then((data)=>{
  console.log(data);
})
```

## 测试RxJS是否可以多次执行

### 服务提供数据

```ts
getRxJSIntervalData(){
  let count = 0;
  return new Observable((observer)=>{
    setInterval(() => {
      let name = '张三-rxJS-interval-' + count;
      observer.next(name);
      count++;
    }, 1000)
})
```

### 组件调用服务

```ts
let streamInterval = this.request.getRxJSIntervalData();
streamInterval.subscribe((data)=>{
  console.log(data);
})
```

# 工具函数

Angular6.x之前使用RxJS的工具函数map和filter，filter过滤数据，map处理数据。

## 使用filter过滤偶数

### 服务提供数据

```ts
getRxJSIntervalNum(){
  let count = 0;
  return new Observable((observer)=>{
    setInterval(() => {
      observer.next(count);
      count++;
    }, 1000)
  })
}
```

### 组件调用数据并过滤

```ts
let streamNum = this.request.getRxJSIntervalNum();
streamNum.pipe(filter((value:any)=>{
  return value % 2 === 0;  // 取偶数
}))
.subscribe((data)=>{
  console.log(data);
})
```

## 使用map处理数据

### 服务提供数据

```ts
getRxJSIntervalNum(){
  let count = 0;
  return new Observable((observer)=>{
    setInterval(() => {
      observer.next(count);
      count++;
    }, 1000)
  })
}
```

### 组件调用服务提供的数据并处理

```ts
let streamNum = this.request.getRxJSIntervalNum();
streamNum.pipe(map((value:any)=>{
  return value * value;  // 做乘法
}))
.subscribe((data)=>{
  console.log(data);
})
```

## map和filter结合使用

### 服务

```ts
getRxJSIntervalNum(){
  let count = 0;
  return new Observable((observer)=>{
    setInterval(() => {
      observer.next(count);
      count++;
    }, 1000)
  })
}
```

### 组件

```ts
let streamNum = this.request.getRxJSIntervalNum();
streamNum.pipe(
  filter((value:any)=>{
    return value % 2 === 0;
  }),
  map((value:any)=>{
    return value * value;
  })
).subscribe((data)=>{
  console.log(data);
})
```

# 详细代码

## 根组件

### app.module.js

```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import { HomeComponent } from './components/home/home.component';

import { RequestService } from './services/request.service';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent
  ],
  imports: [
    BrowserModule
  ],
  providers: [RequestService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### app.component.html

```html
<app-home></app-home>
```

## request服务

### request.service.ts

```ts
import { Injectable } from '@angular/core';

import { Observable } from 'rxjs'

@Injectable({
  providedIn: 'root'
})
export class RequestService {

  constructor() { }

  // 同步方法，测试获取同步数据
  getData(){
    return 'this is service data'
  }

  // 异步方法，测试直接获取异步数据
  getAsyncData(){
    setTimeout(() => {
      let name = '张三-async';
      return name;
    }, 1000)
  }

  // 通过回调函数模拟异步，获取异步数据
  getCallbackData(cb:any){
    setTimeout(() => {
      let name = '张三-callback';
      cb(name);
      return name;
    }, 1000)
  }

  // 通过Promise获取异步数据
  getPromiseData(){
    return new Promise((resolve, reject)=>{
      setTimeout(() => {
        let name = '张三-Promise';
        resolve(name);
      }, 1000)
    })
  }

  // 通过rxJS来处理异步数据
  getRxJSData(){
    return new Observable((observer)=>{
      setTimeout(() => {
        let name = '张三-rxJS';
        observer.next(name);
        //observer.error(name);
      }, 3000)
    })
  }

  // 测试promise多次执行
  getPromiseIntervalData(){
    return new Promise((resolve)=>{
      setInterval(() => {
        let name = '张三-Promise';
        resolve(name);
      }, 1000)
    })
  }

  getRxJSIntervalData(){
    let count = 0;
    return new Observable((observer)=>{
      setInterval(() => {
        let name = '张三-rxJS-interval-' + count;
        observer.next(name);
        count++;
      }, 1000)
    })
  }

  getRxJSIntervalNum(){
    let count = 0;
    return new Observable((observer)=>{
      setInterval(() => {
        observer.next(count);
        count++;
      }, 1000)
    })
  }
}
```

## home组件

### home.component.ts

```ts
import { Component, OnInit } from '@angular/core';

import { RequestService } from 'src/app/services/request.service';

import { map, filter } from 'rxjs/operators';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.scss']
})
export class HomeComponent implements OnInit {

  constructor(public request:RequestService) { 

  }

  ngOnInit(): void {
    // 1.同步方法
    let data = this.request.getData();
    console.log(data);

    // 2.直接获取异步数据, 会返回undefined，说明异步数据无法在外部直接获取
    let callbackData = this.request.getAsyncData();
    console.log(callbackData);

    // 3.通过回调函数获取异步数据
    this.request.getCallbackData((data:any)=>{
      console.log(data)
    });

    // 4.通过Promise获取异步数据
    let promiseData = this.request.getPromiseData();
    promiseData.then((data)=>{
      console.log(data);
    })

    // 5.通过RxJS获取异步方法里面的数据
    let rxJSData = this.request.getRxJSData();
    rxJSData.subscribe((data)=>{
      console.log(data);
    })

    // 6.过1s以后撤回刚才的操作
    let stream = this.request.getRxJSData();
    let d = stream.subscribe((data)=>{
      console.log(data);
    })
    setTimeout(() => {
      d.unsubscribe();  // 取消订阅
    }, 1000)

    // 7.测试promise多次执行（发现只执行了一次，没有这个能力）
    let intervalData = this.request.getPromiseIntervalData();
    intervalData.then((data)=>{
      console.log(data);
    })

    // 8.RxJS执行多次
    // let streamInterval = this.request.getRxJSIntervalData();
    // streamInterval.subscribe((data)=>{
    //   console.log(data);
    // })

    // 9.用工具函数filter对返回数据进行处理
    // let streamNum = this.request.getRxJSIntervalNum();
    // streamNum.pipe(filter((value:any)=>{
    //   return value % 2 === 0;
    // }))
    // .subscribe((data)=>{
    //   console.log(data);
    // })

    //10.用工具函数map对返回数据进行处理
    // let streamNum = this.request.getRxJSIntervalNum();
    // streamNum.pipe(map((value:any)=>{
    //   return value * value;
    // }))
    // .subscribe((data)=>{
    //   console.log(data);
    // })

    //11.map和filter结合使用
    // let streamNum = this.request.getRxJSIntervalNum();
    // streamNum.pipe(
    //   filter((value:any)=>{
    //     return value % 2 === 0;
    //   }),
    //   map((value:any)=>{
    //     return value * value;
    //   })
    // ).subscribe((data)=>{
    //   console.log(data);
    // })
  }

}
```
