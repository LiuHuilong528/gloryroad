# Angular Learning

## 核心知识

### 架构

Angular扩展语法编写 __HTML模板__ , __组件__ 管理模板，__服务__ 添加应用逻辑，__模块__ 打包发布组件和服务。

#### 模块

Angular模块都带有 `@NgModule` 装饰器的类；接收描述模块属性的哦元数据对象：
- `declarations` -声明模块的 **视图类** ——组件、指令和管道。
- `exports` -declarations 的子集，被其他组件使用。
- `imports` -组件需要的类所在的其他模块
- `providers` -服务创建者，加入全局服务列表中，应用于热河部分
- `bootstrap` -指定主视图（根组件），是其他视图的宿主。__根模块__ 才可设置`bootstrap`属性。

``` javascript
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
@NgModule({
  imports:      [ BrowserModule ],
  providers:    [ Logger ],
  declarations: [ AppComponent ],
  exports:      [ AppComponent ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { }

```

##### NgModules vs  JavaScript 模块

NgModules 是Angular的基础特性之一。

JavaScript的模块和Angualar模块系统完全无关；

javascript中一个 __文件__ 是一个模块，文件中的对象从属模块；通过 `export` 关键字将对象声明为公共的，供其他模块使用；

##### Angualr 模块库

每个Angualr库的名字都带有 `@angular` 的前缀；用 npm 包管理工具安装，用 `import` 语句导入组件；

#### 组件

__组件__ 负责控制屏幕的一小块区域——视图；组件同样由属性和方法组成的API于视图交互；

#### 模板

组件视图——是通过组件自带的模板来定义，以HTML形势存在，其中可以使用Angular的模板语法。

#### 元数据

JS类加上Angular的装饰器来附加元数据，即可成为Angular组件。

`@Component` 可以将紧随其后的类标记为组件类；接收配置对象，创建和展示组件及视图：

`@Component` 配置项包括：
- `selector` -CSS选择器，Angular会在 __父级__ HTML中查找 `<hero-list>` 标签，创建并插入。
- `templateUrl` -组件HTML模板的相对地址。
- `providers` -组件所需服务的 __依赖租入提供商数组__

`@Component` 的元数据包含了Angular从哪里获取组件指定的构建块；__模块__、__元数据__ 、__组件__ 共同描绘视图；

#### 数据绑定

Angular支持数据绑定，一种让模板的各部分与组件的各部分相互合作的机制；有四种语法绑定形式;三种绑定方向——绑定到DOM、绑定自DOM以及双向绑定。

``` javascript
// list
<li>{{hero.name}}</li>
<hero-detail [hero]="selectedHero"></hero-detail>
<li (click)="selectHero(hero)"></li>
// detail
<input [(ngModel)]="hero.name">
```

- `{{heroe.name}}` 插值表达式，绑定到DOM
- `[hero]` 属性绑定，用于值在不同组件中的传递
- `(click)` 事件绑定，
- `[(ngModule)]` 双向数据绑定，可以结合属性绑定和事件绑定的功能；双向绑定中，数据属性值通过属性绑定从组件流入输入框；用户修改通过事件绑定流回组件，属性值成为最新的值。

#### 指令

组件是带模板的指令；分：结构性指令和属性型指令。

结构性指令——通过DOM中添加、移除和替换元素修复布局；

``` javascript
<li *ngFor="let hero of heroes"></li>
<hero-detail *ngIf="selectedHero"></hero-detail>
```

- `*ngFor` 为 `heroes` 数组中的每个对象生成一个 `<li>` 标签
- `*ngIf` 只有选择的英雄存在时，才会包含 `HeroDetail` 组件

属性指令——修改现有元素的外观或行为。

``` javascript
<input [(ngModel)]="hero.name">
```

- `ngModel` 修改现有元素行为，设置其显示属性值，响应change事件；

#### 服务

广义范畴：值、函数，或应用所需的特性。

服务是Angular应用的基础。组件是最大的服务消费者。

组件的任务是提供用户体验，介于视图和应用逻辑之间。设计良好的组件为数据绑定提供属性和方法，把其他琐事委托给服务。

#### 依赖注入

依赖注入——提供类的新实例一种方式，负责处理好类所需的全部依赖。

Angular通过 __构造函数的参数类型__ 得知组件需要哪些服务。

注入器维护一个服务实例的容器，存放以前创建的实例。如果所请求的服务实例不在容器中，注入器会创建一个服务实例，并且添加到容器中，然后把服务返回给Angular。当所有请求的服务都被解析完成并返回时，Angular会以这些服务为参数去调用组件的构造函数。这是依赖注入。

注入器创建服务——用注入器为服务注册提供商（provider），由provider 创建或返回服务；providers 可以在根模块上添加，也可在组件 `@Component` 中注册；

依赖注入要点：
- 依赖注入参透整个 Angular框架，随处可见
- 注入器(Injector) 是本机制核心。
  - 注入器负责维护一个容器，用于存放它创建的服务实例。
  - 注入器能使用privider 创建一个新的服务实例。
- privider 是用于创建服务的配方；
- 把privider 注册到注入器。

***


### 模板与数据绑定

#### 显示数据

把 HTML 模板中的控件绑定到 Angular 组件的属性是Angular最典型的数据显示方式。

##### 使用插值表达式显示组件属性

使用差值表达式，就是把属性名包裹在双花括号中放入视图模板中：
```javascript
<h1>{{title}}</h1>
```

##### 内联(inline)模板 or 模板文件

`template` 属性定义为内联的；     
`templateUrls` 属性定义的是模板文件

##### 使用构造函数 or 变量初始化

```javascript
export class AppCtorComponent {
  title: string;
  myHero: string;

  constructor() {
    this.title = 'Tour of Heroes';
    this.myHero = 'Windstorm';
  }
}
```

##### ngFor 显示数组

```javascript
<li *ngFor="let hero of heroes">
    {{ hero }}
</li>
```

##### 创建数据类

```javascript
export class Hero {
  constructor(
    public id: number,
    public name: string) { }
}
```

- 声明了一个构造参数及其类型
- 声明同名的公共属性
- `new` 实例，属性初始化为相应的参数值

#### 模板语法

MVC - 模型-视图-控制器
MVVM- 模型-视图-视图模型

组件是控制器或视图模型，模板是视图。

###### 模板中HTML
HTML是 Angular 模板语言；但是 `<script>` 元素被忽略，防止脚本注入攻击风险。

##### 插值表达式 ( {{...}} )
括号之间的‘素材’，通常是组件属性的名称。

##### 模板表达式

模板表达式很想javascript ，以下javascript表达式在 模板表达式中禁止：
- 赋值 (`=` ,`+=`,`-+`....)
- `new` 运算符
- 使用 `;` 或 `.` 的链式表达式
- 自增或自减操作符( `++` 和 `--` )

###### 表达式上下文

典型表达式上下文是组件实例，是各种绑定值的来源；也能包含组件外的对象（如：模板输入变量和模板引用变量）。

```javascript
  //典型
  {{title}}
  <span [hidden]="isUnchanged">changed</span>

  //组件外的对象
  <div *ngFor="let hero of heroes">{{hero.name}}</div>
  <input #heroInput> {{heroInput.value}}
```

表达式中的上下文由 *模板变量* 、指令 _上下文变量_ 和组件 _成员_ 叠加而成。同时出现的优先级（高-低）：模板变量-指令上下文-组件成员。

模板表到时不能引用全局命名空间任何东西，也不能调用 `console.log` `Math.max` .只能引用表达式上下文的成员。

######　表达式指南

- 没有可见副作用
- 执行速度
- 非常简单
- 幂等性

##### 模板语句

模板语句——响应由绑定目标触发的事件；如：`(event)="statement"`;支持基本赋值和表达式链。

###### 语句上下文


###### 语句指南

##### 绑定语法：概括

| 数据方向 | 语法 |  绑定类型 |
|---------|------|----------|
| 单向：数据源->视图目标 | `{{expression}} [target]="expression" bind-target="expression"` | 插值表达式Property Attribute类样式 |
| 单向：视图-数据源 | `(target)="statement" on-target="statement"` | 事件 |
| 双向 | `[(target)]="expression" bindon-target="expression"` | 双向 |


###### 新的思维模型

###### HTML attribute 与 DOM property 的对比
HTML attribute 初始化化 DOM property ，property 值可以变化；attribute 值不可改变；

Angular 模板绑定是通过 `property` 和 _事件_ 来工作，而不是 `attribute`;

###### 绑定目标

绑定目标：
- 元素 | 组件 | 指令 的property
- 元素 | 组件 | 指令 事件
- 极少 attribute 名

##### 属性绑定（`[属性名]`）

##### 事件绑定( ` (事件名) ` )

#####  ` EventEmitter ` 自定义事件——将事件向父组件弹射

子组件代码：

``` javascript
<!-- html -->
template: `
<div>
  <img src="{{heroImageUrl}}">
  <span [style.text-decoration]="lineThrough">
    {{prefix}} {{hero?.name}}
  </span>
  <button (click)="delete()">Delete</button>
</div>`

<!--ts -->
// This component make a request but it can't actually delete a hero.
deleteRequest = new EventEmitter<Hero>();

delete() {
  this.deleteRequest.emit(this.hero);
}
```

父组件代码：

```javascript
<!-- html -->
<hero-detail (deleteRequest)="deleteHero($event)" [hero]="currentHero"></hero-detail>

<!-- ts -->
  deleteHero(hero: Hero) {
    this.alert(`Delete ${hero ? hero.name : 'the hero'}.`);
  }

```



###### 内置属性型指令
属性型指令会监听和修改其它HTML元素或组件的行为、元素属性（Attribute）、DOM属性（Property）。常用如下：
- `NgClass` -添加或移除CSS类
- `ngStyle` -添加或移除CSS样式
- `NgMolel` -双向绑定HTML表单元素；只能用于表单元素。

###### 内置结构型指令
结构型指令的职责是HTML布局。 它们塑造或重塑DOM的结构，这通常是通过添加、移除和操纵它们所附加到的宿主元素来实现的。常见的有：
- `NgIf`
- `NgFor`
- `NgSwitch`

结构性指令：前缀 `*` 不能少了。

#### 模板引用变量 ( `#var` )

用于引用模板中某个DOM元素，也可以引用Angular组件、指令或WebComponent；

``` html
<input #phone placeholder="phone number">
```

`#phone` 声明一个叫 `phone` 的模板引用变量引用 `<input>` 元素；这样在模板任何地方引用它；

``` html
<input #phone placeholder="phone number">

<!-- lots of other elements -->

<!-- phone refers to the input element; pass its `value` to an event handler -->
<button (click)="callPhone(phone.value)">Call</button>
```

#### 输入输出属性 ( @Input 和 @Output )

绑定目标在 `=` _左侧_ , _源_ 在 `=` 右侧。

绑定目标是绑定符： `[]` , `()` , `[()]` 中的属性或事件名， _源_ 是引号（`" "`）中部分或插值符号（`{{ }}`）中的部分。

源指令中的每个成员都会自动在绑定中可用。不需要特殊处理，就可以在表达式或者语句中访问指令的成员。

访问目标指令中的成员则受 _限制_ 。只能绑定到现实标记为 _输入_ 或 _输出_ 的属性。

###### 声明输入和输出属性

- 装饰器标记属性的输入输出属性     
  ``` javascript
    @Input()  hero: Hero;
    @Output() deleteRequest = new EventEmitter<Hero>();
  ```
- 指令元数据 `inputs` 和 `outputs`     
  ```javascript
      @Component({
        inputs: ['hero'],
        outputs: ['deleteRequest'],
      })
  ```

__以上方式择一使用，不可同时使用__

_输入_ 属性通常接收数据值。 _输出_ 属性暴露事件生产者。输入和输出从目标指令的角度来说的。


###### 输入/输出属性起别名 Alias

```javascript
  /* Way one */
  @Output('myClick') clicks = new EventEmitter<string>(); //  @Output(alias) propertyName = ...
  /* Way two */
  @Directive({
    outputs: ['clicks:myClick']  // propertyName:alias
  })
```

#### 模板表达式操作符

##### 管道操作符（`|`）

```html
  <!-- Pipes -->
  <hr><h2 id="pipes">Pipes</h2>

  <div>Title through uppercase pipe: {{title | uppercase}}</div>

  <!-- Pipe chaining: convert title to uppercase, then to lowercase -->
  <div>
    Title through a pipe chain:
    {{title | uppercase | lowercase}}
  </div>

  <!-- pipe with configuration argument => "February 25, 1970" -->
  <div>Birthdate: {{currentHero?.birthdate | date:'longDate'}}</div>

  <div>{{currentHero | json}}</div>

  <div>Birthdate: {{(currentHero.birthdate | date:'longDate') | uppercase}}</div>

  <div>
    <!-- pipe price to USD and display the $ symbol -->
    <label>Price: </label>{{product.price | currency:'USD':true}}
  </div>
```

##### 安全导航操作符(`?.`)和空属性路径

`?.` 保护属性路径中的 `null` `undefined` 的值：

``` html
  <!-- -->
  The current hero's name is {{currentHero?.name}}

  <!-- No hero, no problem! -->
  The null hero's name is {{nullHero?.name}}

  {{a?.b?.c?.d}}  <!--a.b.c.d-->
```

##### 非空断言操作符（`!`）

```html
<!--No hero, no text -->
  <div *ngIf="hero">
  The hero's name is {{hero!.name}}
</div>
```

***

#### 生命周期钩子

##### 组件生命周期钩子概览

指令和组件生命周期：新建、更新和销毁。
##### 生命周期顺序

Angular使用构造函数新建组件或指令后，会按以下顺序在特定时刻调用生命周期钩子的方法：
1. `ngOnChanges()`
2. `ngOnInit()`
3. `ngDoCheck()`
4. `ngAfterContentInit()`
5. `ngAfterContentChecked()`
6. `ngAfterViewInit()`
7. `ngAfterViewChecked()`
8. `ngOnDestroy()`


##### 接口是可选的（理论上）？
##### 其他生命周期钩子
##### 生命周期练习
##### Peek-a-boo:全部钩子
##### 窥探OnInit和OnDestroy
##### OnChanges()钩子
##### DoCheck()钩子
##### AfterView钩子
##### AfterContent钩子
###### 内容投影
内容投影：从组件外部导入HTML内容，并插入组件模板中指定位置上的一种途径。

存在内容投影的迹象：
- 组件元素标签中有HTML
- 组件模板中出现 `<ng-content>` 标签

***

#### 组件之间的交互

##### 通过输入型绑定把数据从父组件传到子组件

##### 通过setter 截听输入属性值的变化

##### 通过 `ngOnChanges()` 截听输入属性值的变化

##### 父组件监听子组件的事件
子组件暴露 `EventEmitter` 属性，事件发生时，子组件利用该属性 `emits` （向上弹射）事件。父级组件做出响应。


##### 父组件与子组件通过本地变量互动

##### 父组件调用 `@ViewChild()`
父组件的类需要读取子组件的属性值或调用子组件的方法时，就不能使用本地变量方法。此时可以将子组件爱你做为 `ViewChild` 注入父组件中。
##### 父组件与子组件通过服务通讯

***

#### 组件样式

##### 使用组件样式
在组件元数据中设置 `style` 属性：

``` javascript
@Component({
  selector: 'hero-app',
  template: `
    <h1>Tour of Heroes</h1>
    <hero-app-main [hero]=hero></hero-app-main>`,
  styles: ['h1 { font-weight: normal; }']
})
export class HeroAppComponent {
/* . . . */
}
```

##### 特殊的选择器
- `:host` 伪类选择器，选择组件 宿主 元素中的元素（相对组件模板内部的元素）；是以宿主元素为目标的 *唯一* 方式；宿主不是组件模板的一部分，而是父组件模板的一部分。    
  ``` CSS
    :host(.active) {
      border-width: 3px;
    }  
  ```

- `:host-context` 选择器，在当前组件宿主元素的祖先节点查找CSS类，知道文档根节点为止。     
  ```CSS
    <!-- 祖先元素有CSS类 `theme-light` 时才应用此样式-->
    :host-context(.theme-light) h2 {
      background-color: #eef;
    }
  ```

- `/deep/@deprected` ` >> ` ` ::ng-deep `;` deep ` 会强制一个样式对各级子组件视图生效，不但作用域组件的子视图，也作用于组件的内容。     
  ``` css
    :host /deep/ h3 {
      font-style: italic;
    }
  ```
以所有的 ` <h3> ` 元素为目标，从宿主元素到当前元素再到DOM中的所有子元素。

` /depp/ ` 组合器别名： ` >> ` 和 ` ::ng-deep `;CSS标准中 ` shadow-piercing descendant combinator ` 已经被废弃，Angular也将会移除对 ' /deep/ ' ` >> ` ` ng-deep ` 的支持。

##### 样式加载进组件
方式：
- 设置 ` styles ` 或 ` styleUrls ` 元数据
- 内联在模板的HTML中
- 通过 ` css ` 文件导入

##### 控制试图的封装模式：原生（Native）、仿真（Emulated）和无（none）
视图封装模式：
- ` Native ` 使用浏览器的 ` Shadow DOM ` 实现为组件的宿主元素附加一个 Shadow DOM；组件被包裹在此DOM中，不进不出，没有样式能进来，组件样式出不去。
- ` Emulated ` 通过预处理的CSS代码模拟ShadowDOM行为，只进不出，全局样式能进来，组件样式出不去。
- ` None `  Angular 不使用视图封装，能进能出。


```ts
// warning: few browsers support shadow DOM encapsulation at this time
encapsulation: ViewEncapsulation.Native
```

**Shadow DOM** 为web组件中的DOM和CSS提供了封装。使得这些东西与主文档的DOM保持分离

***

#### 动态组件加载器
` ComponentFactoryResolver ` 动态加载组件。
##### 动态组件加载
##### 指令
##### 加载组件
##### 解析组件

##### 公共的AdComponent接口

##### 最终的广告栏

***
#### 属性型指令
##### 指令概览

##### 创建简单的属性指令
##### 使用属性指令
##### 响应用户引发的事件
##### 使用数据绑定向指令传值
##### 绑定到第二个属性
##### 为什么加 `@input`

***

#### 结构型指令

结构型指令可以操作DOM树。
##### 结构指令简介
结构型指令职责是HTML布局。塑造或重塑DOM的结构，如添加、移除或维护这些元素。

结构型指令语法：在指令前加上 ` * ` 号，如：` *nfIf ` ；这个简单写法中的字符串是一个 **微语法** ,不是通常的模板表达式。Angular会解析语法糖，变为 ` <ng-template> `标记。

三个常见内置结构型指令--Ngif、NgFor、NgSwitch。

指令拼写形式： ` NgIf `——指令类名，` ngIf ` ——指令属性名。

##### Ngif分析

移除DOM元素，节省资源，提升性能。
##### 星号（` * `）前缀

` * `--简化更复杂语法的“语法糖”。Angular分两个阶段解析，首先，把` *nfIf="..." `翻译为 ` template ` **属性** ` template="ngIf ..." `；然后，` template ` **属性** 翻译为 ` <nf-template> ` 元素，并包裹宿主元素。

``` html
<div template="ngIf hero">{{hero.name}}</div>

<!--第一步-->
<div template="ngIf hero">{{hero.name}}</div>
<!--第二步-->
<ng-template [ngIf]="hero">
  <div>{{hero.name}}</div>
</ng-template>
```

Angular会在真正渲染的时候填充 `<ng-template>` 的内容，并且把 `<ng-template>` 替换为一个供诊断用的注释。
##### ` *ngFor `内幕

` ngFor ` 全特性应用：

``` html
<div *ngFor="let hero of heroes; let i=index; let odd=odd; trackBy: trackById" [class.odd]="odd">
  ({{i}}) {{hero.name}}
</div>

<div template="ngFor let hero of heroes; let i=index; let odd=odd; trackBy: trackById" [class.odd]="odd">
  ({{i}}) {{hero.name}}
</div>

<ng-template ngFor let-hero [ngForOf]="heroes" let-i="index" let-odd="odd" [ngForTrackBy]="trackById">
  <div [class.odd]="odd">({{i}}) {{hero.name}}</div>
</ng-template>
```

- 微语法——通过简单的、友好的字符串来配置一个指令；微语法解析器把字符串翻译为 ` <ng-template> ` 上的属性；
  - ` let ` 声明模板输入变量；将 ` let hero ` 翻译为 ` let-hero `;
  - 接收 `of` 和 `trackby` ,首字母大写加上指令属性名前缀，最终名字是 `ngForOf` `ngForTrackBy` ; `NgFor`  **输入属性** 数组，而track-by函数是 ` trackById `
  - ` NgFor ` 指令在列表上循环，每次循环灰设置和重置上下文对象的属性： ` index ` 和 ` odd ` 和一个特殊的属性 ` $implicit `(隐式变量)。
  - ` let-i ` 和 ` let-odd ` 变量是通过 ` let i=index ` 和 ` let odd=odd ` 来定义。Angular设置它们为上下文对象中的index和 odd 属性值。
  - 上线下文中的属性 `let-hero` 没有指定过，实际来自一个隐式变量。Angular会把 ` let-hero ` 设置为上下文对象中的 ` $implicit `属性，`NgFor` 会用当前迭代中的英雄初始化它。

- 模板输入变量。模板输入变量和模板引用变量是不同的：作用域，语法，它们有独立的命名空间。
- 每个宿主元素只能有一个结构型指令

##### ` NgSwitch ` 内幕
##### 优先使用星号(` * `)语法
##### ` <ng-template> ` 指令
##### 使用 ` <ng-container> `把兄弟元素归为一组
##### 写一个结构性指令
##### 总结














//Angular