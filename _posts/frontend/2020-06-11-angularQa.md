---
layout: post
title:  "angular——QA"
date:   2020-06-11  +0800
categories: frontend-js
tag: 知识点
---

* content
{:toc}

# angular——QA

## 注解(装饰器) & 指令

### 注解(装饰器)
  
@xxxx ,本质是一个函数，return fn ,TS的特性

### 自定义装饰器

```typescript
@FilterMoney() money = 1000;
```

```typescript
export function FilterMoney() {
  const toMoney = num => {
    num = num.toFixed(2);
    num = parseFloat(num);
    num = num.toLocaleString();
    return num; //返回的是字符串23,245.12保留2位小数
  };
  return (target: Object, key: string) => {
    let val = target[key];
    const getter = () => {
      return val;
    };
    const setter = (value: string) => {
      val = toMoney(value);
    };
    Object.defineProperty(target, key, {
      get: getter,
      set: setter,
      enumerable: true,
      configurable: true,
    });
  };
}
```

### 指令

结构性如：`ngIf`   `ngFor`  ...

属性指令： `ngClass`  `ngStyle` `ngModel` ...

### 自定义指令

参考文档

## 绑定

- 事件绑定
   `(click)="handleClick()"`

- 样式绑定
  
  `[ngStyle]="{ 'background-color': color }"`,
  
  `[ngStyle]="{ index === selectedIndex ? activeColor : color }"`,

  `[ngClass]="{ class1: xxx, class2: xxx }"`

- 双向绑定

```html

<!-- FormsModule 中提供的指令 ngModel 可以简化双向绑定的流程 -->
<input [(ngModel)]="username" />

<!-- Angular 中的双向绑定其实就是属性绑定+事件绑定-->
username:string,
<input [value]="username" (input)="username = $event.target.value" />


```
  
## 组件

### 生命周期

```typescript

constructor(){} // 构造函数首先被调用

// 以下是钩子函数

ngOnChanges(){} // (多次)输入属性变化是被调用   即  @Input 值发生变化时

ngOnInit(){} // （一次）组件初始化时被调用,并且只被调用。

ngDoCheck(){} // （多次）脏值检测时被调用  

    ngAfterContentInit(){} // 在组件使用 ng-content 指令的情况下，Angular 会在将外部内容放到视图后用。它主要用于获取通过 @ContentChild 或 @ContentChildren 属性装饰器查询的内容视图元素。

    ngAfterContentChecked(){} //在组件使用 ng-content 指令的情况下，Angular 会在检测到外部内容的绑定或者每次变化的时候调用

    ngAfterViewInit(){} // 在组件相应的视图初始化之后调用，它主要用于获取通过 @ViewChild 或 @ViewChildren 属性装饰器查询的视图元素。

    ngAfterViewChecked(){} // 组件每次检查视图时调用

ngOnDestory(){} // 组件销毁时调用

// 指令与组件共有的钩子  ngOnChanges,ngOnInit,ngDoCheck,ngOnDestroy
// ngAfterContentInit,ngAfterContentChecked,ngAfterViewInit,ngAfterViewChecked

```

```typescript
import {
  Component,
  OnInit,
  Input,
  Output,
  EventEmitter,
  SimpleChanges,
  OnChanges,
  DoCheck,
  AfterContentInit,
  AfterContentChecked,
  AfterViewInit,
  AfterViewChecked,
  OnDestroy,
} from "@angular/core";

@Component({
  selector: "app-components",
  templateUrl: "./components.component.html",
  styleUrls: ["./components.component.scss"],
})
export class ComponentsComponent
  implements
    OnInit,
    OnChanges,
    AfterContentInit,
    AfterContentChecked,
    AfterViewInit,
    AfterViewChecked,
    OnDestroy {
  @Input() title;
  /**
   * 构造函数永远首先被调用
   */
  constructor() {
    console.log("constructor调用");
  }
  /**
   * 组件 `@Input` 属性发生变化时调用
   * @param changes
   */
  ngOnChanges(changes: SimpleChanges): void {
    // console.log(changes);
    console.log("ngOnChanges调用:" + this.title);
  }
  /**
   * 组件中嵌套的 `<ng-content>` 的内容初始化完成
   */
  ngAfterContentInit(): void {
    console.log("ngAfterContentInit调用");
  }
  /**
   * 组件中嵌套的 `<ng-content>` 的内容的变化脏值检查
   */
  ngAfterContentChecked(): void {
    console.log("ngAfterContentChecked调用");
  }
  /**
   * 组件视图渲染完成，可以安全的操作视图中的元素
   */
  ngAfterViewInit(): void {
    console.log("ngAfterViewInit调用");
  }
  /**
   * 组件视图的脏值检查
   */
  ngAfterViewChecked(): void {
    console.log("ngAfterViewChecked调用");
  }

  ngDoCheck(): void {
    console.log("ngDoCheck调用:" + this.title);
  }

  /**
   * 组件销毁时调用，一般发生在父组件 ngIf 或路由变化时
   */
  ngOnDestroy(): void {
    console.log("ngOnDestroy调用");
  }
  /**
   * 组件初始化完成时 调用
   */
  ngOnInit() {
    console.log("ngOnInit调用");
  }
}


```


[![mq8EHP.png](https://s2.ax1x.com/2019/08/29/mq8EHP.png)](https://imgchr.com/i/mq8EHP)

### 模板在组件中引用

```html
<p #contentP>
  components works!
  <span>{{ title }}</span>
  <span>!!!!</span>
  

  <span #span *ngFor="let item of items; let index = index">
    {{ item.name }}</span
  >
</p>
<button (click)="handleColor()">更换颜色</button>
```

```typescript
 import {
  Component,
  OnInit,
  AfterViewInit,
  ViewChild,
  ElementRef,
  QueryList,
  ViewChildren,
  Renderer2,
} from "@angular/core";

 // static  true/false  (ng-if  ng-for   ->   false)
 @ViewChild("contentP", { static: true }) contentPRef: ElementRef;

 @ViewChildren("span") spans: QueryList<ElementRef>;


 constructor(private rd2: Renderer2) {}

// 事件操作
 handleColor() {
    this.contentPRef.nativeElement.querySelector("span").style.color = "red";
    // document.querySelector("span").style.color = "red";
 }

// 页面加载后如果需要控制dom
ngAfterViewInit(): void {
    document.querySelector("span").style.color = "red";

    this.spans.forEach(item => {
      this.rd2.setStyle(item.nativeElement, "color", "#f60");
    });
}
```

@ViewChild   选择器   用来查找引用的dom元素或者组件
ElementRef 是DOM元素的一个包装类 ， 由于DOM元素不是Angular中的类，因此需要一个包装类在Angular中使用标识其类型

[Renderer2](https://www.angular.cn/api/core/Renderer2)

Renderer2类是Angular以service的形式提供的抽象，允许操作应用程序的元素而无需直接触摸DOM。




### 组件传值

- 父组件向子组件传值（@Input）

```typescript
// 父组件
actId = "533299723979243520"

//子组件
// <app-history-list [actId]="actId" ></app-history-list>

@Input() actId;

```  
  
- 子组件向父组件传值 (@Output)
  
```typescript
// 子组件
import { Output, EventEmitter } from "@angular/core";

@Output() historyChange = new EventEmitter<number>();

this.historyChange.emit(data);

// 父组件 

// <app-history-list
// (historyChange)="handleHistory($event)
// ></app-history-list>

handleHistory(data) {
    this.currentHisyory = data;
}

```  
  
- 子组件向子组件组件传值

1. 利用  子组件A（@Output）  ->  父组件(@Input) -> 子组件B
2. 利用service作为中间存储

```typescript
// 创建service

import { Injectable } from "@angular/core";

@Injectable({
  providedIn: "root",
})
export class StoreService {
  // 数据
  questionStore;

  constructor() {}
}

```

```typescript
// 子组件A
import { StoreService } from "../services/store.service";

constructor(
    private store: StoreService,
  ) {}

this.store.questionStore = this.data;

```

```typescript
// 子组件B
import { StoreService } from "../services/store.service";

constructor(
    private store: StoreService,
  ) {}

this.data = this.store.questionStore;

```

- 补充 子组件A值发生变化，子组件B如何接收？
  
```typescript
import {
  Component,
  OnInit,
  OnChanges,
  SimpleChanges,
} from "@angular/core";
import { StoreService } from "../services/store.service";

export class QustionPanelComponent implements OnInit, OnChanges{

    data:string;

    constructor(
        private store: StoreService
    ) {}

    ngOnChanges(changes: SimpleChanges): void {
        //组件传值第一种方式
        if (changes["currentHisyory"]) {
            this.data = changes["currentHisyory"]["currentValue"];
        }

        //组件传值第二种方式
        if (this.store.questionStore) {
            this.data = this.store.questionStore;
        }
    }
}

```
  
### 投影

- ng-content  动态内容

表现形式  `<ng-content select="class or 标签 or 指令"></ng-content>` ,类似于vue  slot

使用场景  自定义组件时使用

## 路由

- URL

```typescript
//url http://localhost:4200/menu?from=home

//配置
{
  path:':menu',
  component:'MenuComponent'
}

//html使用
<a [routerLink]="['/menu']" [queryParams]={from:'home'}>xxx</a>

//ts使用
import { Router } from '@angular/router';
constructor(private router: Router) {}
this.router.navigate(['menu',{queryParams:{from:'home'}}])

//读取

```
  
- 参数

```typescript
import { ActivatedRoute } from '@angular/router';
constructor(private route: ActivatedRoute) {}
// 获取参数
this.route.queryParamMap.subscribe(params => {
  console.log(params.get("menu"));
})
```
  
## 管道

Pipe在视图层面提供便利的变化方法，如 `Date->3小时前`  ，`1222.33 -> ￥1,222.33`

在 Angular 2+ 中，已经没有了 filter 过滤器，取而代之的是 pipe 管道

例如：`uppercase` ,`json`, `date`, `currency`

```typescript
// 定义
obj={
  name:'xxxx',
  age:'20',
  sex:'man'
}
date: Date = new Date()
price = 12
arr=[1,2,3,4,5]


//使用
{{ obj | json}}
{{ date | date: 'MM-dd'}}  {{ date | date: 'yyyy-MM-dd'}}
{{ price | currency}}  {{ price | currency: 'CNY'}}  

```

自定义管道
```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({ name: 'appAgo' })
export class AgoPipe implements PipeTransform {
  transform(value: any): any {
    if (value) {
      const seconds = Math.floor((+new Date() - +new Date(value)) / 1000);
      if (seconds < 29) {
        // 小于 30 秒
        return '刚刚';
      }
      const intervals = {
        年: 3600 * 24 * 365,
        月: 3600 * 24 * 30,
        周: 3600 * 24 * 7,
        天: 3600 * 24,
        小时: 3600,
        分钟: 60,
        秒: 1
      };
      let counter = 0;
      for (const unitName in intervals) {
        if (intervals.hasOwnProperty(unitName)) {
          const unitValue = intervals[unitName];
          counter = Math.floor(seconds / unitValue);
          if (counter > 0) {
            return counter + ' ' + unitName + '前';
          }
        }
      }
    }
    return value;
  }
}

// 注入
import { AgoPipe } from './pipes';
@NgModule({
  declarations: [
    AgoPipe
  ],
  exports: [
    AgoPipe
  ]
})

// 使用 {{ date | appAgo }}


```

## 依赖注入

- 提供服务
  1. @Injectable()  
  2. 标记为可供注入的服务

- 模块中声明
  1. import对应模块
  2. providers 数组
    ```typescript
        providers: [
            { provide: NZ_I18N, useValue: zh_CN },
            { provide: LocationStrategy, useClass: HashLocationStrategy },
            httpInterceptorProviders
        ],
        // useValue

        // useClass-指明了使用的类,通过声明的类进行注入 可以简写成 providers: [className]




    ```

- 在组建中使用
  1. 构造函数声明 `private route: ActivatedRoute` 
  2. 自定义service  `private service: HistoryService`

使用场景  `service`
  

## 脏值检查

1. 数据改变时更新视图
2. 触发场景： 浏览器事件，http请求，定时器
3. 检查两个状态值: 当前状态vs更新状态

## rxjs

操作全是异步的

状态：next  error complete

```typescript
this.route.queryParamMap.subscribe(params => {
  // next
},err =>{
  // error  
}, ()=>{
  // complete
})
```

- 操作符&Async
  
```typescript

menu$: Observable<string>;
data$: Observable;

this.menu$ = this.route.paramMap.pipe(
    filter(params => params.has('menu')),
    map(params => params.get('menu'))
);

this.data$ = this.service.getData()

```

```html
 <div *ngIf="(menu$ | async) === 'home'; else other">
    <ul>
        <li *ngFor="let item of data$ | async">{{item.name}}</li>
    </ul>
 </div>

 <ng-template #other>
  Other works
</ng-template>
```



## Q&A

1. Q: 大 JSON 在详情页面渲染  A.B.C.D   报错？
   
   A:
    ```
    {{ A?.B?.C?.D }}
   ```

2. Q: ngModel 中 A.B.C.D 报错?
   
   A: 在父级标签使用ngIf
