---
title: Angular学习（4）之Angular组件基础使用
date: 2022-08-18 14:34:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Angular组件基础使用

1. `ng g component components/home`，`app.module.ts`文件会自动添加HomeComponent组件
2. `app.component.html`文件添加标签`<app-home></app-home>`
3. `app.module.ts`手动添加FormsModule组件，测试表单数据双向绑定，即MVVM，注意imports
4. `home.component.scss`的简单样式不再罗列，可以自行补入
5. `home.component.ts`代码如下
   ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-home',
      templateUrl: './home.component.html',
      styleUrls: ['./home.component.scss']
    })
    export class HomeComponent implements OnInit {

      public title:string = "This is a title"

      public picUrl:string='https://images.pexels.com/photos/33287/dog-viszla-close.jpg?auto=compress&cs=tinysrgb&w=1260&h=750&dpr=1'

      public list:any[]=[{
        "title": "我是新闻1"
      },{
        "title": "我是新闻2"
      },{
        "title": "我是新闻3"
      }]

      public flag:boolean = true

      public orderStatus:number = 4
      //public orderStatus:string = '1'

      public attr:string = 'blue'

      public today:any = new Date()

      public keywords:string = 'This is a default value'

      constructor() { 
        console.log(this.today)
      }

      ngOnInit(): void {
      }

      run(): void {
        alert("执行方法")
      }

      getData(): void{
        alert(this.title)
      }

      setData(): void{
        this.title = 'Hello, I am a title.'
      }

      runEvent(e: any){
        let dom:any = e.target;
        dom.style.color = "red"
      }

      keyDown(e: any){
        console.log("user keydown, type key is:" + e.key)
        console.log(e)
        if(e.keyCode == 13){
          console.log('You has type the Enter key.')
        }
        console.log('You has input string: ' + e.target.value)
      }

      keyUp(e: any){
        if(e.keyCode == 13){
          console.log('You has type the Enter key.')
        }
      }

      changeKeywords(){
        this.keywords = 'change the value'
      }

      getKeywords(){
        console.log(this.keywords)
      }
    }
   ```
6. `home.component.html`，代码如下
   ```html
    <h1>引入图片</h1>
    <img src="assets/images/01.jpg" alt="收藏">
    <img [src]="picUrl" alt="">


    <hr>
    <h1>循环数据 显示数据的索引（key）</h1>
    <ul>
        <li *ngFor="let item of list; let key=index">&#123;&#123;key+1&#125;&#125; --- &#123;&#123;item.title&#125;&#125;</li>
    </ul>


    <hr>
    <h1>条件判断语句: if</h1>
    <div *ngIf="flag">
        <img src="assets/images/01.jpg" alt="收藏">
    </div>
    <div *ngIf="!flag">
        <img [src]="picUrl" alt="">
    </div>
    <ul>
        <li *ngFor="let item of list;let key=index;" >
            <span *ngIf="key==1" class="red">
                &#123;&#123;key&#125;&#125; - &#123;&#123;item.title&#125;&#125;
            </span>
            <span *ngIf="key!=1">
                &#123;&#123;key&#125;&#125; - &#123;&#123;item.title&#125;&#125;
            </span>
        </li>
    </ul>


    <hr>
    <h1>条件判断语句: switch</h1>
    <div [ngSwitch]="orderStatus">
        <div *ngSwitchCase="1">已支付</div>
        <div *ngSwitchCase="2">未支付</div>
        <div *ngSwitchCase="3">支付失败</div>
        <div *ngSwitchCase="4">取消</div>
        <div *ngSwitchCase="5">已发货</div>
        <div *ngSwitchDefault>output2</div>
    </div>


    <hr>
    <h1>属性: ngClass,ngStyle</h1>
    <div class="red">
        ngClass演示（尽量不要用dom来改变class）
    </div>
    <div [ngClass]="{'blue': flag, 'red': !flag}">
        ngClass演示
    </div>
    <strong>循环数组，让数组的第一个元素的样式为red</strong>
    <li *ngFor="let item of list;let key=index;" [ngClass]="{'red': key==0, 'orange': key==1, 'blue': key==2}">
        &#123;&#123;key&#125;&#125; --- &#123;&#123;item.title&#125;&#125;
    </li>
    <p [ngStyle]="{'color': 'red'}">我是一个p标签，测试ngStyle</p>
    <p [ngStyle]="{'color': attr}">绑定数据到ngStyle</p>


    <hr>
    <h1>管道</h1>
    <p>&#123;&#123;today&#125;&#125;</p>
    <p>管道：&#123;&#123;today | date: "yyyy-MM-dd HH:mm:ss"&#125;&#125;</p>
    <p>还可以自定义管道方法</p>


    <hr>
    <h1>自定义事件</h1>
    <strong>Title: &#123;&#123;title&#125;&#125;</strong><br>
    <button (click)="run()">执行事件</button><br>
    <button (click)="getData()">执行事件，获取数据</button><br>
    <button (click)="setData()">执行事件，改变数据</button><br>
    <button (click)="runEvent($event)">执行方法获取事件对象</button><br>


    <hr>
    <h1>表单事件 事件对象</h1>
    <!-- <input type="text" (keydown)="keyDown()"> -->
    KeyDown: <input type="text" (keydown)="keyDown($event)"><br>
    KeyUp: <input type="text" (keyup)="keyUp($event)">


    <hr>
    <h1>双向数据绑定（MVVM）只是针对表单</h1>
    <input type="text" [(ngModel)]="keywords"><br><br>
    &#123;&#123;keywords&#125;&#125;<br><br>
    <button (click)="changeKeywords()">改变绑定值</button><br><br>
    <button (click)="getKeywords()">获取当前绑定值</button><br><br>
   ```

