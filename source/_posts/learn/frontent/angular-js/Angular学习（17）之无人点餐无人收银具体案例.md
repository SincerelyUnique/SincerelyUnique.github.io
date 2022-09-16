---
title: Angular学习（17）之无人点餐无人收银具体案例
date: 2022-09-15 08:15:43
tags:
categories:
- [学习, 前端知识学习, AngularJS]
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# 准备

```bash
PS D:\angular> ng new angulardemo13
PS D:\angular> cd angulardemo13
PS D:\angular\angulardemo13> ng g component components/home
PS D:\angular\angulardemo13> ng g component components/pcontent
PS D:\angular\angulardemo13> ng g service services/common
```

# 代码
## app根组件及全局样式
### app.module.ts
```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { HomeComponent } from './components/home/home.component';
import { PcontentComponent } from './components/pcontent/pcontent.component';

import { HttpClientModule,HttpClientJsonpModule } from '@angular/common/http';

import { CommonService } from './services/common.service';

@NgModule({
  declarations: [
    AppComponent,
    HomeComponent,
    PcontentComponent
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule,
    HttpClientJsonpModule
  ],
  providers: [CommonService],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### app.component.html
```html
<router-outlet></router-outlet>
```

### app-routing.module.ts
```ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';

import { HomeComponent } from './components/home/home.component';
import { PcontentComponent } from './components/pcontent/pcontent.component';

const routes: Routes = [
  {
    path: 'home', 
    component: HomeComponent
  },
  {
    path: 'pcontent/:id', 
    component: PcontentComponent
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
@charset "UTF-8";
body, div, ul, li, ol, h1, h2, h3, h4, h5, h6, input, textarea, select, p, dl, dt, dd, a, img, button, form, table, th, tr, td, tbody, article, aside, details, figcaption, figure, footer, header, hgroup, menu, nav, section {
  margin: 0;
  padding: 0; }

html {
  font-size: 62.5%;
  /* 根元素是10px;     16*62.5%=10  默认在pc端根元素会解析成12px    */ }

body {
  font: 12px/1.5 'Microsoft YaHei','宋体', Tahoma, Arial, sans-serif;
  color: #555;
  background-color: #F7F7F7; }

em, i {
  font-style: normal; }

ul, li {
  list-style-type: none; }

strong {
  font-weight: normal; }

.clearfix:after {
  content: "";
  display: block;
  visibility: hidden;
  height: 0;
  clear: both; }

/*# sourceMappingURL=basic.css.map */
```

## common服务
### common.service.ts
```ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class CommonService {

  public domain:string='http://a.itying.com/';

  constructor(public http:HttpClient) { }

  get(api:string){
    return new Promise((resolve,reject)=>{
        this.http.get(this.domain+api).subscribe((response)=>{
            resolve(response);
        })  
    })
  }
}
```

## home组件
### home.component.html
```html
<header class="index_header">
  <div class="hlist">
    <img src="assets/images/rexiao.png"/>
    <p>热销榜</p>				
  </div>
  <div class="hlist">
    <img src="assets/images/caidan.png" />
    <p>点过的菜</p>				
  </div>
  <div class="hlist">
    <img src="assets/images/sousuo.png"/>
    <p>搜你喜欢</p>				
  </div>
</header>			

<div class="content">
      <div class="item" *ngFor="let item of list">        
        <h3 class="item_cate">&#123;&#123;item.title&#125;&#125;</h3>
        
        <ul class="item_list">
          <li *ngFor="let food of item.list">	
            <div class="inner">
              <a [routerLink]="[ '/pcontent', food._id ]">
                <img [src]="domain+food.img_url" />
                <p class="title">&#123;&#123;food.title&#125;&#125;</p>						
                <p class="price">¥&#123;&#123;food.price&#125;&#125;</p>
              </a>
            </div>		
          </li>
        </ul>
      </div>
</div>
<div class="bg" id="bg">
  
</div>
```

### home.component.scss
```scss
@charset "UTF-8";
.index_header {
  width: 96%;
  margin: 0 auto;
  height: 4.4rem;
  background: #fff;
  margin-top: 1rem;
  display: flex;
  border-radius: .5rem; }
  .index_header .hlist {
    flex: 1;
    text-align: center;
    padding-top: .2rem;
    border-right: 1px solid #eee; }
    .index_header .hlist img {
      width: 2rem;
      height: 2rem;
      margin: 0 auto; }
    .index_header .hlist:last-child {
      border-right: none; }

/*列表*/
.item .item_cate {
  text-align: center;
  padding: .5rem; }
.item .item_list {
  display: flex;
  flex-wrap: wrap;
  padding: 0px .5rem; }
  .item .item_list li {
    width: 33.3%;
    padding: .5rem;
    box-sizing: border-box; }
    .item .item_list li .inner {
      background: #fff;
      width: 100%;
      border-radius: .5rem;
      overflow: hidden; }
      .item .item_list li .inner img {
        width: 100%; }
      .item .item_list li .inner p {
        padding: .2rem .5rem; }
      .item .item_list li .inner .title {
        font-weight: bold; }

/*侧边栏*/
.left_cate {
  /*css3运动  加过渡效果*/
  transition: all 1s;
  transform: translate(-100%, 0);
  z-index: 2;
  width: 6rem;
  height: 100%;
  position: fixed;
  background: #eee;
  top: 0px;
  left: 0px; }
  .left_cate ul {
    position: absolute;
    height: 100%;
    padding: .5rem;
    z-index: 3;
    background: #eee; }
    .left_cate ul li {
      line-height: 4.4rem; }
  .left_cate .nav_cate {
    position: absolute;
    right: -3.5rem;
    background: rgba(132, 128, 128, 0.9);
    top: 42%;
    width: 5rem;
    height: 4rem;
    text-align: center;
    border-radius: 0rem 50% 50% 0rem;
    z-index: 2; }
    .left_cate .nav_cate img {
      width: 1.8rem;
      height: 1.8rem;
      margin-left: 1rem;
      margin-top: .4rem; }
    .left_cate .nav_cate p {
      color: #fff;
      margin-left: 1rem;
      margin-top: -0.3rem; }

/*透明层*/
.bg {
  position: fixed;
  width: 100%;
  height: 100%;
  background: rgba(132, 128, 128, 0.4);
  left: 0px;
  top: 0px;
  z-index: 1;
  display: none; }

/*首页导航*/
.footer_nav {
  height: 4.4rem;
  width: 4.4rem;
  background: #000;
  position: fixed;
  color: #fff;
  bottom: .5rem;
  left: .5rem;
  text-align: center;
  border-radius: 50%; }
  .footer_nav img {
    width: 1.8rem;
    height: 1.8rem;
    margin-top: .4rem; }
  .footer_nav p {
    position: relative;
    top: -0.2rem; }

/*导航弹出层*/
.footer_nav_show {
  width: 100%;
  height: 100%;
  position: fixed;
  top: 0px;
  left: 0px;
  background: rgba(0, 0, 0, 0.6);
  z-index: 2; }
  .footer_nav_show .list li {
    height: 4.4rem;
    width: 4.4rem;
    background: #000;
    position: absolute;
    color: #fff;
    left: .5rem;
    text-align: center;
    border-radius: 50%; }
    .footer_nav_show .list li img {
      width: 1.8rem;
      height: 1.8rem;
      margin-top: .4rem; }
    .footer_nav_show .list li p {
      position: relative;
      top: -0.2rem;
      font-size: 1rem; }
    .footer_nav_show .list li:nth-child(1) {
      bottom: 15.4rem;
      left: 0px; }
    .footer_nav_show .list li:nth-child(2) {
      bottom: 12.4rem;
      left: 30%;
      margin-left: -2.2rem; }
    .footer_nav_show .list li:nth-child(3) {
      bottom: 7.4rem;
      left: 45%;
      margin-left: -2.2rem; }
    .footer_nav_show .list li:nth-child(4) {
      left: 50%;
      margin-left: -2.2rem;
      bottom: .5rem; }
    .footer_nav_show .list li:nth-child(5) {
      left: .5rem;
      bottom: .5rem; }

.footer_cart {
  height: 4.4rem;
  width: 4.4rem;
  background: red;
  position: fixed;
  color: #fff;
  bottom: .5rem;
  right: .5rem;
  text-align: center;
  border-radius: 50%; }
  .footer_cart img {
    width: 1.8rem;
    height: 1.8rem;
    margin-top: .4rem; }
  .footer_cart p {
    position: relative;
    top: -0.2rem; }

/*# sourceMappingURL=index.css.map */
```

### home.component.ts
```ts
import { Component, OnInit } from '@angular/core';
import { CommonService } from '../../services/common.service';

@Component({
  selector: 'app-home',
  templateUrl: './home.component.html',
  styleUrls: ['./home.component.scss']
})
export class HomeComponent implements OnInit {

  public list:any[]=[];

  public domain:string='';

  constructor(public common:CommonService) { 
    this.domain=this.common.domain;
  }

  ngOnInit(): void {
    var api='api/productlist';
    this.common.get(api).then((response:any)=>{
      console.log(response);
      this.list=response.result;
    })
  }

}
```

## pcontent组件
### pcontent.component.html
```html
<div class="back"><a [routerLink]="['/home']">返回</a></div>
		
<div class="p_content">		
	<div class="p_info">		
		<img [src]="domain+list.img_url" />
		<h2>&#123;&#123;list.title&#125;&#125;</h2>				
		<p class="price">&#123;&#123;list.price&#125;&#125;/份</p>
	</div>
	<div class="p_detial">
		<h3>
			商品详情					
		</h3>
		<div class="p_content" [innerHTML]='list.content'> 
		</div>
	</div>
</div>
<footer class="pfooter">
	<div class="cart">				
		<strong>数量:</strong>
		<div class="cart_num">
			<div class="input_left">-</div>
			<div class="input_center">
				<input type="text"  readonly="readonly" value="1" name="num" id="num" />
			</div>
			<div class="input_right">+</div>				      
		</div>								
	</div>
	<button class="addcart">加入购物车</button>			
</footer>
```

### pcontent.component.scss
```scss
@charset "UTF-8";
.back {
  height: 3.8rem;
  line-height: 3.8rem;
  width: 3.8rem;
  border-radius: 50%;
  background: #000;
  position: fixed;
  top: .5rem;
  left: .5rem;
  color: #fff; }

  .back a{
    color: #fff;
    display: inline-block; 
    text-decoration: none;
  }
  
  .back:before {
    content: "";
    display: block;
    width: .8rem;
    height: .8rem;
    border-left: .2rem solid #fff;
    border-bottom: .2rem solid #fff;
    float: left;
    position: relative;
    top: 1.3rem;
    left: .6rem;
    transform: rotate(45deg);
    margin-right: .4rem; }

.p_content .p_info {
  background: #fff; }
  .p_content .p_info img {
    width: 100%;
    height: 18rem; }
  .p_content .p_info h2 {
    padding: .2rem .5rem; }
  .p_content .p_info .price {
    padding: .2rem .5rem;
    color: red; }
.p_content .p_detial {
  background: #fff;
  margin-top: 1rem; }
  .p_content .p_detial h3 {
    padding: .5rem; }
  .p_content .p_detial .p_content {
    padding: 1rem; }
    .p_content .p_detial .p_content img {
      max-width: 100%;
      display: block;
      margin: 0 auto; }
    .p_content .p_detial .p_content * {
      line-height: 1.5;
      color: #666; }

/*搴曢儴*/
.pfooter {
  position: fixed;
  bottom: 0px;
  height: 4.4rem;
  line-height: 4.4rem;
  background: #fff;
  left: 0px;
  width: 100%;
  border-top: 1px solid #eee; }
  .pfooter .cart {
    float: left;
    display: flex; }
    .pfooter .cart strong {
      flex: 1;
      font-size: 1.6rem;
      padding: 0rem .5rem; }
    .pfooter .cart .cart_num {
      width: 10rem;
      display: flex;
      margin-top: .8rem; }
      .pfooter .cart .cart_num .input_left, .pfooter .cart .cart_num .input_right {
        flex: 1;
        width: 2.8rem;
        height: 2.8rem;
        line-height: 2.8rem;
        text-align: center;
        color: red;
        border: 1px solid #eee;
        font-size: 2.4rem; }
      .pfooter .cart .cart_num .input_center {
        flex: 1; }
        .pfooter .cart .cart_num .input_center input {
          width: 2rem;
          text-align: center;
          width: 100%;
          height: 2.8rem;
          border: none;
          border-top: 1px solid #eee;
          border-bottom: 1px solid #eee;
          float: left; }
  .pfooter .addcart {
    float: right;
    background: red;
    color: #fff;
    height: 3rem;
    border: none;
    padding: 0 .5rem;
    border-radius: .5rem;
    margin-top: .8rem;
    margin-right: .5rem; }

/*# sourceMappingURL=pcontent.css.map */
```

### pcontent.component.ts
```ts
import { Component, OnInit } from '@angular/core';
import { ActivatedRoute } from '@angular/router';
import { CommonService } from '../../services/common.service';

@Component({
  selector: 'app-pcontent',
  templateUrl: './pcontent.component.html',
  styleUrls: ['./pcontent.component.scss']
})
export class PcontentComponent implements OnInit {

  public domain:string='';

  public list:any;

  constructor(public route:ActivatedRoute,public common:CommonService) {
    this.domain=this.common.domain;
   }

  ngOnInit() {
    this.route.params.subscribe((value:any)=>{
      this.requestContent(value.id)
    })
  }

  requestContent(id:any){
    //请求数据  http://a.itying.com/api/productcontent?id=5ac1a22011f48140d0002955
    var api='api/productcontent?id='+id;
    this.common.get(api).then((response:any)=>{
      console.log(response.result[0]);
      this.list=response.result[0];
    })
  }
}
```

参考：https://github.com/luomt0818/lesson16.git
