---
title: Angular学习（5）之 Angular表单、Dom获取表单值、以及双向数据绑定
date: 2022-08-20 14:34:43
tags:
categories:
valine:
  placeholder: "1. 提问前请先仔细阅读本文档⚡\n2. 页面显示问题💥，请提供控制台截图📸或者您的测试网址\n3. 其他任何报错💣，请提供详细描述和截图📸，祝食用愉快💪"
---

# Angular表单、Dom获取表单值、以及双向数据绑定

1. `ng new angulardemo04`
2. `ng g component components/form`
3. app.module.ts
    ```ts
    import {FormsModule} from '@angular/forms'
    @NgModule({
      declarations: [
        AppComponent,
        FormComponent
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
4. app.component.html
    ```
    <app-form></app-form>
    ```
5. style.scss
    ```scss
    /* You can add global styles to this file, and also import other style files */
    *{
        margin: 0px;
        padding: 0px;
    }
    ul, ol{
        list-style-type: none;
    }
    ```
6. form.components.ts
    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-form',
      templateUrl: './form.component.html',
      styleUrls: ['./form.component.scss']
    })
    export class FormComponent implements OnInit {


      public peopleInfo:any = {
        username: 'Jalen',
        sex: '2',
        cityList: ['北京', '上海', '深圳'],
        city: '北京',
        hobby:[{
          title: '吃饭',
          checked: false
        },{
          title: '睡觉',
          checked: false
        },{
          title: '敲代码',
          checked: true
        }],
        mark: ''
      }

      constructor() { }

      ngOnInit(): void {
      }

      doSubmit(e:any){
        //1.jquery dom操作
        // let nameDom:any = document.getElementById("username")
        // alert(nameDom.value)

        //2.双向数据绑定
        console.log(this.peopleInfo)
      }
    }
    ```
7. form.component.scss
    ```scss
    h2{
        text-align: center;
    }
    .people_list{
        width: 400px;
        margin: 40px auto;
        
        padding: 20px;
        border: 1px solid #eee;

        li{
            height: 50px;
            line-height: 50px;

            .form_input{
                width: 300px;
                height: 30px;
            }
        }

        .submit{
            width: 100px;
            height: 30px;

            float: right;
            margin-right: 50px;
            margin-top: 70px;
        }

        .pre-view{
            margin-top: 30px;
        }
    }
    ```
8. form.component.html
    ```html
    <h2>人员登记系统</h2>

    <div class="people_list">
        <ul>
            <li>姓 名：
                <input type="text" id="username" [(ngModel)]="peopleInfo.username" value="form_input"/>
            </li>

            <li>性 别：
                <input type="radio" value="1" name="sex" id="sex1" [(ngModel)]="peopleInfo.sex"/><label for="sex1">男</label>
                &nbsp;&nbsp;
                <input type="radio" value="2" name="sex" id="sex2" [(ngModel)]="peopleInfo.sex"/><label for="sex2">女</label>
            </li>

            <li>城 市：
                <select name="city" id="city" [(ngModel)]="peopleInfo.city">
                    <option [value]="item" *ngFor="let item of peopleInfo.cityList">&#123;&#123;item&#125;&#125;</option>
                </select>
            </li>

            <li>爱 好：
                <span *ngFor="let item of peopleInfo.hobby;let key=index">
                    <input type="checkbox" [id]="'check' + key" [(ngModel)]="item.checked"/><label [for]="'check' + key">
                        &#123;&#123;item.title&#125;&#125;
                    </label>
                    &nbsp;&nbsp;
                </span>
            </li>

            <li>备 注：
                <!-- <input type="textarea" name="mark" id="mark" [(ngModel)]="peopleInfo.mark" cols="30" rows="10"/> -->
                <textarea name="mark" id="mark" [(ngModel)]="peopleInfo.mark" rows="4" cols="50"></textarea>
            </li>
        </ul>



        <button (click)="doSubmit($event)" class="submit">获取表单的内容</button>

        <br>
        <br>
        <pre class="pre-view">
            &#123;&#123;peopleInfo | json&#125;&#125;
        </pre>
    </div>
    ```

