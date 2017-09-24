# AngularCLI

## 安装 ` nvm ` - Node Version Manager
- `curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.4/install.sh | bash`
- ` nvm install node `  -安装 node
- ` nvm run --v ` - 运行node的版本
- ` npm install -g @angular/cli ` 安装  AngularCLI


## AngularCLI 使用
```shell
# 创建项目
ng new PROJECT-NAME
cd PROJECT-NAME
ng serve

#  运行项目 host 主机 port 端口号
ng serve --host 0.0.0.0 --port 4201

# component
ng generate component my-new-component
ng g component my-new-component # using the alias

# components support relative path generation
# if in the directory src/app/feature/ and you run
ng g component new-cmp
# your component will be generated in src/app/feature/new-cmp
# but if you were to run
ng g component ../newer-cmp
# your component will be generated in src/app/newer-cmp
# if in the directory src/app you can also run
ng g component feature/new-cmp
# and your component will be generated in src/app/feature/new-cmp

```

这样创建的 ` components `, ` directives `  ` pipes `  会自动添加到 ` app.module.ts  ` 中。


### 更新AngularCLI
要先卸载现有的，在更新

```bash

# uninstall
npm uninstall -g angular-cli
npm uninstall --save-dev angular-cli

# 更新
npm uninstall -g @angular/cli
npm cache clean
npm install -g @angular/cli@latest

```

全局更新之后，本地的项目包也要更新

```
rm -rf node_modules dist # use rmdir /S/Q node_modules dist in Windows Command Prompt; use rm -r -fo node_modules,dist in Windows PowerShell
npm install --save-dev @angular/cli@latest
npm install
```



***



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

` [ngSwitch]="hero?.emotion" ` NgSwitch本身不是一个结构指令，而是 **属性指令** ，可以控制 **结构型指令** ` NgSwitchCase ` 和 ` NgSwitchDefault `

##### 优先使用星号(` * `)语法
##### ` <ng-template> ` 指令

` ng-template ` 是Angular元素，渲染HTML；永远不会直接显示出来。在渲染视图前，Angular会把 ` <ng-template> ` 及内容替换成注释。没有使用结构型指令，仅仅把别的元素包装进 ` <ng-template> ` 中，则元素为不可见的。

##### 使用 ` <ng-container> `把兄弟元素归为一组

` <ng-container> ` 是一个分组元素，不会污染样式或元素布局，因为 Angular **不会将它放进DOM中** 。

```html
  <div>
    Pick your favorite hero
    (<label><input type="checkbox" checked (change)="showSad = !showSad">show sad</label>)
  </div>
  <select [(ngModel)]="hero">
    <ng-container *ngFor="let h of heroes">
      <ng-container *ngIf="showSad || h.emotion !== 'sad'">
        <option [ngValue]="h">{{h.name}} ({{h.emotion}})</option>
      </ng-container>
    </ng-container>
  </select>
```

##### 写一个结构性指令

创建指令：
- 导入 ` Directive ` 装饰器
- 导入符号 ` input `  ` TemplateRef ` 和 ` ViewContainerRef `
- 指令类添加装饰器
- 设置 CSS **属性选择器**，标识指令应用的元素

```javascript
  import { Directive, Input, TemplateRef, ViewContainerRef } from '@angular/core';

  /**
   * Add the template content to the DOM unless the condition is true.
   */
  @Directive({ selector: '[myUnless]'})// 选择器放在 []中
  export class UnlessDirective {
    private hasView = false;

    constructor(
      private templateRef: TemplateRef<any>,// TemplateRef 内嵌视图的内容
      private viewContainer: ViewContainerRef) { }  // 视图容器的引用

    @Input() set myUnless(condition: boolean) {
      if (!condition && !this.hasView) {
        this.viewContainer.createEmbeddedView(this.templateRef);
        this.hasView = true;
      } else if (condition && this.hasView) {
        this.viewContainer.clear();
        this.hasView = false;
      }
    }
  }
```

这个指令会在Angular中生成 ` <ng-template> ` 元素中创建一个 **内嵌的视图**  ，插入一个 **视图容器** 中，紧挨着本指令的原来宿主元素（宿主的兄弟节点，而不是子节点）。

##### 总结

***

#### 管道
管道——使数据的展现为对人类更友好的格式。
##### 使用管道

``` html
<p>The hero's birthday is {{ birthday | date }}</p>
```


##### 内置管道

` DatePipe ` 、 ` UpperCasePipe ` 、 ` LowerCasePipe ` ` CurrencyPipe ` ` PercentPipe `
##### 管道参数化
单个参数： ` currency: 'EUR' `
多个参数之间用 ` : ` 分开： ` slice:1:5 `

管道格式 *参数绑定* 到 *组件* ` format ` 属性上：

```html
template: `
  <p>The hero's birthday is {{ birthday | date:format }}</p>
  <button (click)="toggleFormat()">Toggle Format</button>
`
```

```typescript
export class HeroBirthday2Component {
  birthday = new Date(1988, 3, 15); // April 15, 1988
  toggle = true; // start with true == shortDate

  get format()   { return this.toggle ? 'shortDate' : 'fullDate'; }
  toggleFormat() { this.toggle = !this.toggle; }
}
```

##### 链式管道

```html
  The chained hero's birthday is
  {{ birthday | date | uppercase}}
```

##### 自定义管道

```ts
import { Pipe, PipeTransform } from '@angular/core';
/*
 * Raise the value exponentially
 * Takes an exponent argument that defaults to 1.
 * Usage:
 *   value | exponentialStrength:exponent
 * Example:
 *   {{ 2 | exponentialStrength:10 }}
 *   formats to: 1024
*/
@Pipe({name: 'exponentialStrength'})
export class ExponentialStrengthPipe implements PipeTransform {
  transform(value: number, exponent: string): number {
    let exp = parseFloat(exponent);
    return Math.pow(value, isNaN(exp) ? 1 : exp);
  }
}
```

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'power-booster',
  template: `
    <h2>Power Booster</h2>
    <p>Super power boost: {{2 | exponentialStrength: 10}}</p>
  `
})
export class PowerBoosterComponent { }
```

- 管道带有“管道元数据”装饰器的类
- 实现 ` PipeTransform ` 接口的 ` transform ` 方法，接收输入值和可选参数，返回转换后的值。
- ` @Pipe ` 表示这是管道，从 ` core ` 中引入。
- ` @Pipe ` 装饰器可以定义管道名称，被用在模板表达式中。

**注意点**     
- 自定义管道的使用和内置管道完全相同
- 在 ` AppModule ` 的 ` declarations ` 包含这个管道

##### 能力倍增计算器

示例把管道和使用 ` ngModel ` 的双向绑定组合了

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'power-boost-calculator',
  template: `
    <h2>Power Boost Calculator</h2>
    <div>Normal power: <input [(ngModel)]="power"></div>
    <div>Boost factor: <input [(ngModel)]="factor"></div>
    <p>
      Super Hero Power: {{power | exponentialStrength: factor}}
    </p>
  `
})
export class PowerBoostCalculatorComponent {
  power = 5;
  factor = 1;
}
```


##### 管道与变更检测
Angular 通过 *变更检测* 过程来查找绑定值的变更，并在每一次javascript事件之后运行：按键、鼠标移动、定时器以及服务器的响应。变更检测可能会很昂贵，Angular会尽量降低这种成本。

######  无管道

使用变更检测策略检测和更新 ` heroes `数组中的每个英雄：

```html
New hero:
  <input type="text" #box
          (keyup.enter)="addHero(box.value); box.value=''"
          placeholder="hero name">
  <button (click)="reset()">Reset</button>
  <div *ngFor="let hero of heroes">
    {{hero.name}}
  </div>
```

和模板相伴的组件类有英雄数组，能把新的英雄添加到数组中，还可以重置数组。

```ts
export class FlyingHeroesComponent {
  heroes: any[] = [];
  canFly = true;
  constructor() { this.reset(); }

  addHero(name: string) {
    name = name.trim();
    if (!name) { return; }
    let hero = {name, canFly: this.canFly};
    this.heroes.push(hero);
  }

  reset() { this.heroes = heroes.slice(); }
}
```

###### **会飞的英雄** 管道(FlyingHeroesPipe)
` FlyingHeroesPipe ` 管道可以过滤会飞的英雄：

```ts
import { Pipe, PipeTransform } from '@angular/core';

import { Flyer } from './heroes';

@Pipe({ name: 'flyingHeroes' })
export class FlyingHeroesPipe implements PipeTransform {
  transform(allHeroes: Flyer[]) {
    return allHeroes.filter(hero => hero.canFly);
  }
}
```

**引用** 是Angular关心的： *对象的引用变化了，Angular才可以检测到值的变化*

#### 纯(pure)管道与非纯(impure)管道

默认情况下管道都是纯的，通过管道的 ` pure ` 标志属性设置为 ` false ` 制做一个非纯管道

```ts
@Pipe({
  name: 'flyingHeroesImpure',
  pure: false
})
```

###### 纯管道
Angular 只有检测到输入值发生了 *纯变更* 时才会执行 *纯管道* 。 *纯变更* 是指原始类型(String Number Boolean Symbol)值得变更；或者对象的引用的变更。

**注意：** Angular 会忽略(复合)对象的 *内部* 的更改。

###### 非纯管道

Angular会在每个组件变更检测周期中执行 *非纯管道* 。非纯管道可被调用很多次，和每个按键或每次鼠标移动一样频繁。

在本例中可以直接从 ` FlyingHeroesPipe ` 继承来实现 *非纯管道* ` FlyingHeroesImpurePipe `

```ts
@Pipe({
  name: 'flyingHeroesImpure',
  pure: false
})
export class FlyingHeroesImpurePipe extends FlyingHeroesPipe {}
```


###### 非纯 ` AsyncPipe `
Angular的 ` AsyncPipe ` 有趣的非纯管道例子。 ` AsyncPipe ` 接收 ` Promise ` 或者 ` Obserable ` 做为输入，自动订阅这个输入，最终返回它们给出的值。

 ` AsyncPipe ` 管道是有状态的。 该管道维护着一个所输入的 ` Obserable ` 的订阅，并且持续从那个 ` Obserable ` 中发出新到的值。

```ts
import { Component } from '@angular/core';

import { Observable } from 'rxjs/Observable';
import 'rxjs/add/observable/interval';
import 'rxjs/add/operator/map';
import 'rxjs/add/operator/take';

@Component({
  selector: 'hero-message',
  template: `
    <h2>Async Hero Message and AsyncPipe</h2>
    <p>Message: {{ message$ | async }}</p>
    <button (click)="resend()">Resend</button>`,
})
export class HeroAsyncMessageComponent {
  message$: Observable<string>;

  private messages = [
    'You are my hero!',
    'You are the best hero!',
    'Will you be my hero?'
  ];

  constructor() { this.resend(); }

  resend() {
    this.message$ = Observable.interval(500)
      .map(i => this.messages[i])
      .take(this.messages.length);
  }
}
```

这个Async管道节省了组件的样板代码。 组件不用订阅这个异步数据源，而且不用在被销毁时取消订阅(如果订阅了而忘了反订阅容易导致隐晦的内存泄露)。

###### 一个非纯而且带缓存的管道

一个向服务器发起HTTP请求的管道。

```ts
import { Pipe, PipeTransform } from '@angular/core';
import { Http }                from '@angular/http';

import 'rxjs/add/operator/map';

@Pipe({
  name: 'fetch',
  pure: false
})
export class FetchJsonPipe  implements PipeTransform {
  private cachedData: any = null;
  private cachedUrl = '';

  constructor(private http: Http) { }

  transform(url: string): any {
    if (url !== this.cachedUrl) {
      this.cachedData = null;
      this.cachedUrl = url;
      this.http.get(url)
        .map( result => result.json() )
        .subscribe( result => this.cachedData = result );
    }

    return this.cachedData;
  }
}
```

演示：

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'hero-list',
  template: `
    <h2>Heroes from JSON File</h2>

    <div *ngFor="let hero of ('heroes.json' | fetch) ">
      {{hero.name}}
    </div>

    <p>Heroes as JSON:
      {{'heroes.json' | fetch | json}}
    </p>`
})
export class HeroListComponent { }
```

请求数据的过程：
- 每个绑定都有它自己的管道实例。
- 每个管道实例都缓存了它自己的URL和数据。
- 每个管道实例都只调用一次服务器。


###### JsonPipe
` JsonPipe ` 把数据转为JSON格式，内置管道。

###### 纯管道与纯函数
**纯函数** 是指在处理输入并返回结果时，不会产生任何副作用的函数。 给定相同的输入，它们总是返回相同的输出。

一个 **纯管道** 必须总是用 **纯函数** 实现。

##### 附录：没有 *FilterPipe* 或者 *OrderByPipe*

Angular没有这2个管道：
- 性能堪忧
- 阻止激进的代码最小化


***
#### 动画
##### 概述

 > Angular动画基于标准的 Web动画API构建，在支持此API的浏览器中会用原生方式工作。

> 其他的则需要 ployfill , animations.min.js


##### 起步范例

##### 状态与转场

##### 示例：进场与离场

##### 范例：不同状态下进场和离场


##### 可动的(Animatable)属性与单位


##### 自动属性值计算

##### 动画时间线

##### 基于关键帧(Keyframes)的多阶段动画

##### 并行动画(Group)


##### 动画回调



***
### 表单
#### 用户输入

使用Angular事件绑定语法将DOM事件绑定到事件处理器。

##### 绑定到用户输入事件

将DOM事件的名字包裹在圆括号中，用放在引号中模板语法对它赋值；

```ts
<button (click)="onClickMe()">Click me!</button>
```

##### 通过 ` $event ` 获取用户输入

监听 ` keyup ` 事件，将整个事件 ` $event ` 传递给组件处理器

```ts
template: `
  <input (keyup)="onKey($event)">
  <p>{{values}}</p>
`


-----------

export class KeyUpComponent_v1 {
  values = '';

  onKey(event: any) { // without type info
    this.values += event.target.value + ' | ';
  }
}
```

所有的DOM事件对象都有 ` target ` 属性，引用触发该事件的元素。

**注意：** 不要把整个DOM事件传入方法中，只有需要知道了解这些细节是才去提取这些信息。

##### 从一个模板引用变量中获得用户输入

使用Angular的模板引用变量是另一种获取用户数据的方式——(` #teampleVar `)

```ts
@Component({
  selector: 'loop-back',
  template: `
    <input #box (keyup)="0">
    <p>{{box.value}}</p>
  `
})
export class LoopbackComponent { }
```

**注意一定要绑定事件否则无法正常工作**



##### 按键事件过滤(通过 key.enter )
Angular的 ` key.enter ` 模拟事件——当用户敲回车时，Angular才会调用事件处理器。


##### 失去焦点事件(blur)

` <input (blur)="update(box.value)"> `

##### 把它们放在一起

```ts
@Component({
  selector: 'little-tour',
  template: `
    <input #newHero
      (keyup.enter)="addHero(newHero.value)"
      (blur)="addHero(newHero.value); newHero.value='' ">

    <button (click)="addHero(newHero.value)">Add</button>

    <ul><li *ngFor="let hero of heroes">{{hero}}</li></ul>
  `
})
export class LittleTourComponent {
  heroes = ['Windstorm', 'Bombasto', 'Magneta', 'Tornado'];
  addHero(newHero: string) {
    if (newHero) {
      this.heroes.push(newHero);
    }
  }
}
```


##### 源代码


##### 小结


***
#### 模板驱动表单


##### 模板驱动的表单

##### 搭建

##### 创建Hero模型类

##### 创建表单组件

##### 修改app.module.ts

##### 修改app.component.ts

##### 创建初始HTML表单模板

##### 用 ` ngFor ` 添加超能力

##### 使用 ` ngModel ` 进行双向数据绑定

##### 通过 ngModel 跟踪修改状态与有效性验证

##### 添加用于视觉反馈的自定义 CSS




##### 显示和隐藏验证错误信息




##### 使用 ngSubmit 提交该表单

##### 切换两个表单区域（额外的奖励）


***
#### 表单验证

##### 模板驱动验证
模板驱动验证表单添加验证机制，需要添加验证属性；可以通过 ` ngModel ` 导出成局部模板变量来查看空间的状态：

``` html
<input id="name" name="name" class="form-control"
       required minlength="4" forbiddenName="bob"
       [(ngModel)]="hero.name" #name="ngModel" >

<div *ngIf="name.invalid && (name.dirty || name.touched)"
     class="alert alert-danger">

  <div *ngIf="name.errors.required">
    Name is required.
  </div>
  <div *ngIf="name.errors.minlength">
    Name must be at least 4 characters long.
  </div>
  <div *ngIf="name.errors.forbiddenName">
    Name cannot be Bob.
  </div>

</div>
```
##### 响应式表单的验证
应该在组件类中直接报验证器函数添加到表单控件模型上（` FormControl `）

###### 验证器函数
2种验证器函数：    
  - 同步验证器函数： 接收一个空件实例，返回一组验证错误或null，可在实例化  ` FormControl ` 时作为构造函数的第二个参数传进去。
  - 异步验证函数：接收空间实例，返回 ` Promise ` 或 ` Obserable ` 对象。稍后返回一组验证错误或 ` null ` ,实例化 ` FormControl ` 的第三个构造参数。

  **注意：** 所有的同步验证器通过后，Angular在运行异步验证器。每一个验证器执行完后，才会设置验证错误。


###### 内部验证器
模板驱动表单中可用的属性型验证器（` required ` ` minlength `）对应于 ` Validators ` 类中的同名函数。

响应式表单——可以使用内置验证器，但是使用函数形式：

``` ts
ngOnInit(): void {
  this.heroForm = new FormGroup({
    'name': new FormControl(this.hero.name, [
      Validators.required,
      Validators.minLength(4),
      forbiddenNameValidator(/bob/i) // <-- Here's how you pass in the custom validator.
    ]),
    'alterEgo': new FormControl(this.hero.alterEgo),
    'power': new FormControl(this.hero.power, Validators.required)
  });
}

get name() { return this.heroForm.get('name'); }

get power() { return this.heroForm.get('power'); }
```

注意：
- 可以通过把函数放入数组中传入，这样支持多中验证器
- 响应式表单中，通过表单所属的控件组(` FormGroup `) 的 ` get ` 方法访问表单控件，也会为模板定义一些getter做为间断形式。

##### 自定义验证器
内部验证器不可适用所有场所，还是的创建自定义的验证器；` forbiddenNameValidator ` 函数的代码：
``` ts
/** A hero's name can't match the given regular expression */
export function forbiddenNameValidator(nameRe: RegExp): ValidatorFn {
  return (control: AbstractControl): {[key: string]: any} => {
    const forbidden = nameRe.test(control.value);
    return forbidden ? {'forbiddenName': {value: control.value}} : null;
  };
}
```

###### 添加响应式表单
直接将验证器函数传给 ` FormControl ` 即可：

###### 添加到模板驱动表单

1. 自定义的 **验证器函数**
2. 自定义验证器函数的包装器——如： ` ForbiddenValidatorDirective `
3. 选择器添加到输入框中就可以激活验证器

``` ts
@Directive({
  selector: '[forbiddenName]',
  providers: [{provide: NG_VALIDATORS, useExisting: ForbiddenValidatorDirective, multi: true}]
})
export class ForbiddenValidatorDirective implements Validator {
  @Input() forbiddenName: string;

  validate(control: AbstractControl): {[key: string]: any} {
    return this.forbiddenName ? forbiddenNameValidator(new RegExp(this.forbiddenName, 'i'))(control)
                              : null;
  }
}
```

``` html
<input id="name" name="name" class="form-control"
       required minlength="4" forbiddenName="bob"
       [(ngModel)]="hero.name" #name="ngModel" >
```



##### 表示控件状态的 ` CSS `类
Angular自动把控件属性做为CSS类映射到控件所在的元素，方便添加元素样式：
- ` ng-valid `
- ` ng-invalid `
- ` ng-pending `
- ` ng-pristing `
- ` ng-dirty `
- ` ng-untouched `
- ` ng-touched `

``` CSS
.ng-valid[required], .ng-valid.required  {
  border-left: 5px solid #42A948; /* green */
}

.ng-invalid:not(form)  {
  border-left: 5px solid #a94442; /* red */
}
```


***
#### 响应式表单
##### 响应式表单简介
Angualr2种构建表单技术：
- 响应式表单
- 模板驱动表单

以上都属于 ` @angular/forms ` 库，并共享一组公共的表单控件，但设计哲学、编程风格和具体技术上显著不同；都有自己的模块： ` ReactiveFormsModule ` 和 ` FormsModule `

**异步 VS 同步** ：响应式表单是同步的，模板驱动表单是异步的；


##### 准备工作
##### 创建数据模型
##### 创建响应式表单组件
##### 创建模板
##### 导入 ` ReactiveFormsModule `
##### 显示 ` HeroDetailComponent `

###### 基础表单类

- ` AbstractControl ` 是三个具体表单类的抽象类，为它们提供共同的行为和属性，其中有些事可观察对象（ ` Obserable `）
- ` FormControl ` 跟踪一个 *单独* 表单控件的值和有效性状态。对应一个HTML表单控件，如：下拉框和输入框。
- ` FormGroup ` 跟踪一组 ` AbstractControl ` 的实例的值和有效性状态。该组的属性中包含了它的子控件。组件中的顶级表单就是 ` FormGroup `
- ` FormArray ` 跟踪 ` AbstractControl ` 实例组成的有序数组和有效性状态。


##### 添加 ` FormGroup `

##### 表单模型概览

###### ` FormBuilder ` 简介
` FormBuilder ` 在处理控件创建的细节问题帮助减少重复劳动：

``` ts
export class HeroDetailComponent3 {
  heroForm: FormGroup; // <--- heroForm is of type FormGroup

  constructor(private fb: FormBuilder) { // <--- inject FormBuilder
    this.createForm();
  }

  createForm() {
    this.heroForm = this.fb.group({
      name: '', // <--- the FormControl called "name"
    });
  }
}
```

``` HTML
<h2>Hero Detail</h2>
<h3><i>A FormGroup with multiple FormControls</i></h3>
<form [formGroup]="heroForm" novalidate>
  <div class="form-group">
    <label class="center-block">Name:
      <input class="form-control" formControlName="name">
    </label>
  </div>
  <div class="form-group">
    <label class="center-block">Street:
      <input class="form-control" formControlName="street">
    </label>
  </div>
  <div class="form-group">
    <label class="center-block">City:
      <input class="form-control" formControlName="city">
    </label>
  </div>
  <div class="form-group">
    <label class="center-block">State:
      <select class="form-control" formControlName="state">
          <option *ngFor="let state of states" [value]="state">{{state}}</option>
      </select>
    </label>
  </div>
  <div class="form-group">
    <label class="center-block">Zip Code:
      <input class="form-control" formControlName="zip">
    </label>
  </div>
  <div class="form-group radio">
    <h4>Super power:</h4>
    <label class="center-block"><input type="radio" formControlName="power" value="flight">Flight</label>
    <label class="center-block"><input type="radio" formControlName="power" value="x-ray vision">X-ray vision</label>
    <label class="center-block"><input type="radio" formControlName="power" value="strength">Strength</label>
  </div>
  <div class="checkbox">
    <label class="center-block">
      <input type="checkbox" formControlName="sidekick">I have a sidekick.
    </label>
  </div>
</form>
<p>Form value: {{ heroForm.value | json }}</p>
```

 注意 ` formGroupName ` 和 ` formControlName ` 属性。 他们是Angular指令，用于把相应的HTML控件绑定到组件中的 *FormGroup* 和 *FormControl* 类型的属性上。


###### 多级 ` FromGroup `

` address ` 是个名称为 ' address ' 的子 ` FormGroup `

``` ts
export class HeroDetailComponent5 {
  heroForm: FormGroup;
  states = states;

  constructor(private fb: FormBuilder) {
    this.createForm();
  }

  createForm() {
    this.heroForm = this.fb.group({ // <-- the parent FormGroup
      name: ['', Validators.required ],
      address: this.fb.group({ // <-- the child FormGroup
        street: '',
        city: '',
        state: '',
        zip: ''
      }),
      power: '',
      sidekick: ''
    });
  }
}
```

HTML中 ` <div formGroupName="address" class="well well-lg">..</div> ` 的是 address 的 ` FormGroup `

``` html
<h2>Hero Detail</h2>
<h3><i>A FormGroup with multiple FormControls</i></h3>
<form [formGroup]="heroForm" novalidate>
  <div class="form-group">
    <label class="center-block">Name:
      <input class="form-control" formControlName="name">
    </label>
  </div>
  <div formGroupName="address" class="well well-lg">
    <h4>Secret Lair</h4>
    <div class="form-group">
      <label class="center-block">Street:
      <input class="form-control" formControlName="street">
    </label>
    </div>
    <div class="form-group">
      <label class="center-block">City:
      <input class="form-control" formControlName="city">
    </label>
    </div>
    <div class="form-group">
      <label class="center-block">State:
      <select class="form-control" formControlName="state">
          <option *ngFor="let state of states" [value]="state">{{state}}</option>
      </select>
    </label>
    </div>
    <div class="form-group">
      <label class="center-block">Zip Code:
      <input class="form-control" formControlName="zip">
    </label>
    </div>
  </div>
  <div class="form-group radio">
    <h4>Super power:</h4>
    <label class="center-block"><input type="radio" formControlName="power" value="flight">Flight</label>
    <label class="center-block"><input type="radio" formControlName="power" value="x-ray vision">X-ray vision</label>
    <label class="center-block"><input type="radio" formControlName="power" value="strength">Strength</label>
  </div>
  <div class="checkbox">
    <label class="center-block">
      <input type="checkbox" formControlName="sidekick">I have a sidekick.
    </label>
  </div>
</form>
<p>Form value: {{ heroForm.value | json }}</p>
```

##### ` FormBulder ` 简介
##### 查看 ` FormControl ` 的属性

``` html
<!-- 获取 FormControl 的状态 -->
<p>Name value: {{ heroForm.get('name').value }}</p>
<!-- 获取 FromGroup 中的 FormControl 的状态 -->
<p>Street value: {{ heroForm.get('address.street').value}}</p>
```

##### 数据模型和表单模型

*数据模型* 的值复制到 *表单模型* 中哦你，注意：
- 理解数据模型映射到表单模型中的属性
- 修改的数据流是从 DOM 流向表单模型，表单控件永远不会修改数据模型



##### 使用 ` setValue ` 和 ` patchValue ` 操纵表单模型

` setValue ` 和 ` patchValue ` 可用在控件初始化或重置上。

###### ` setValue ` 方法
` setValue ` *立即* 设置 *每个表单* 控件的值，数据模型与表单模型要匹配

``` ts
this.heroForm.setValue({
  name:    this.hero.name,
  address: this.hero.addresses[0] || new Address()
});
```

` setValue ` 会在赋值前检查数据对象的值，任何与 ` FormGroup `结构不同或缺少表单组件中任何一个空间的数据对象都不会接收；会有错误信息返回，而 ` patchValue ` 会默默失败。

` setValue ` 会捕获错误，并报告。

###### ` patchValue ` 方法

` patchValue ` 可以修改 ` FormGroup ` 控件中部分属性值，可以解决数据模型和表单模型之间的差异，不会检查缺失的控件值，不会抛出有用的错误信息。

``` ts
this.heroForm.patchValue({
  name: this.hero.name
});
```

##### 使用 ` FormArray ` 来表示 ` FormGroup ` 数组

` FormArray ` 可以显示的是一个数组的 ` FormGroup `

使用 ` FormArray ` :
- 数组中定义条目（` FormGroup ` 和 ` FromControl `）
- 数组初始化为一组从数据模型中的数据创建的条目
- 根据需求添加或移除条目


``` ts
this.heroForm = this.fb.group({
  name: ['', Validators.required ],
  secretLairs: this.fb.array([]), // <-- secretLairs as an empty FormArray
  power: '',
  sidekick: ''
});

setAddresses(addresses: Address[]) {
  const addressFGs = addresses.map(address => this.fb.group(address));
  const addressFormArray = this.fb.array(addressFGs);
  this.heroForm.setControl('secretLairs', addressFormArray);
}
```

` FormGroup.setControl `方法，不是 ` setValue ` 方法设置值，因为我们要替换的是 *控件* ,而不是 *控件值* 。

**注意：** ` FormGroup.get('FormArrayName') as FormArray; ` 可以获得 ` FormArray ` .
FormArray 的展示：

``` html
<div formArrayName="secretLairs" class="well well-lg">
  <div *ngFor="let address of secretLairs.controls; let i=index" [formGroupName]="i" >
    <!-- The repeated address template -->
    <h4>Address #{{i + 1}}</h4>
    <div style="margin-left: 1em;">
      <div class="form-group">
        <label class="center-block">Street:
          <input class="form-control" formControlName="street">
        </label>
      </div>
      <div class="form-group">
        <label class="center-block">City:
          <input class="form-control" formControlName="city">
        </label>
      </div>
      <div class="form-group">
        <label class="center-block">State:
          <select class="form-control" formControlName="state">
            <option *ngFor="let state of states" [value]="state">{{state}}</option>
          </select>
        </label>
      </div>
      <div class="form-group">
        <label class="center-block">Zip Code:
          <input class="form-control" formControlName="zip">
        </label>
      </div>
    </div>
    <br>
    <!-- End of the repeated address template -->
  </div>
</div>
```

##### 监视控件的变化
` ngOnChanges ` 只会检查变量是否被重新赋值，变量的属性值变化则不会检查；
监测表单控件值得变化，可以使用 ` FormControl.valueChanges `

``` ts
nameChangeLog: string[] = [];
logNameChange() {
  const nameControl = this.heroForm.get('name');
  nameControl.valueChanges.forEach(
    (value: string) => this.nameChangeLog.push(value)
  );
}
```

``` html
<h4>Name change log</h4>
<div *ngFor="let name of nameChangeLog">{{name}}</div>
```

##### 保存表单的数据
##### 总结


***
#### 动态表单

##### 程序启动
##### 问卷问题模型
##### 问卷表单组件
##### 问卷数据
##### 动态模板





***
### 引导启动

``` ts
import { NgModule }      from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { AppComponent }  from './app.component';

@NgModule({
  imports:      [ BrowserModule ],
  declarations: [ AppComponent ],
  bootstrap:    [ AppComponent ]
})
export class AppModule { }
```

` @NgModule `装饰器将 ` AppModule `标记为Angular模块类；` @NgModule `参数：
- ` imports ` ——引入应用在浏览器运行需要的 **模块** ; 只能有 ` NgModule `类不要放其他的类。
- ` declarations `——声明模块的 **组件** ，包括根组件
- ` bootstrap  `——声明 **根组件** ，Angular会创建并插入到 ` index.html ` 页面中
- ` providers ` ——声明依赖，服务提供类- **服务类**

#### ` imports `数组
#### ` declarations ` 数组
` declarations ` 告诉Angular 哪些组件属于本模块；

>只有\*可以声明的 — *组件* 、 *指令* 和 *管道*  — 属于 ` declarations ` 数组。 不要将其他类型的类添加到 `declarations` 中，例如 ` NgModule` 类, 服务类，模型类。


#### ` bootstrap `数组
` bootstrap ` 数组在 引导启动过程中，将数组的组件类创建组件，然后插入浏览器DOM中。

#### ` main。ts `引导
JIT——即时编译器

引导JIT的浏览器应用的推荐地点是 ` src/main.ts `文件
```ts
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';
import { AppModule }              from './app/app.module';

platformBrowserDynamic().bootstrapModule(AppModule);
```

#### 关于Angular模块更多知识

***
### NgModules
#### NgModule
##### Angular模块 (NgModule)
##### Angular模块化

##### ` AppModule ` -应用的根模块
##### 在 ` main.ts ` 中引导
Angular提供了许多引导选项,以下是其中的二种：

**即时（JiT）编译** -动态编译

``` ts
// The browser platform with a compiler
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

// The app module
import { AppModule } from './app/app.module';

// Compile and launch the module
platformBrowserDynamic().bootstrapModule(AppModule);
```


**预编译器（AoT-Ahead-Of-Time）** -静态编译

静态方案能生成更小、启动更快的应用，推荐优先使用。

使用 *静态* 选项，Angular编译器作为构建流程的一部分提前运行，生成一组类工厂。核心是： ` AppModuleNgFactory `

```ts
// The browser platform without a compiler
import { platformBrowser } from '@angular/platform-browser';

// The app module factory produced by the static offline compiler
import { AppModuleNgFactory } from './app/app.module.ngfactory';

// Launch with the app module factory.
platformBrowser().bootstrapModuleFactory(AppModuleNgFactory);
```

无论是 ` JiT ` 还是 ` AoT ` 编译器都会从同一份 ` AppModule ` 源码中生成一个 ` AppModuleNgFactory ` 类。 ` JiT ` 编译器动态地在浏览器的内存中创建这个工厂类。 ` AoT ` 编译器把工厂输出成一个物理文件，也就是我们在静态版本 ` main.ts ` 中导入的那个。

##### 声明指令和组件

##### 服务提供商

##### 导入支持性模块
2个指令同名时，在 ` import ` 时使用 ` as ` 关键字为第二指令创建别名就可以：

``` ts
import {
  HighlightDirective as ContactHighlightDirective
} from './contact/highlight.directive';
```

##### 解决指令冲突
**注意** :特性模块可以消除组建与指令的冲突。特性模块可以把来自一个模块中的声明和来自另一个的区分隔开。
##### 特性模块

特性模块可以解决的问题:
- ` AppModule ` 减负，避免它变大
- 解决指令冲突
- 明确特性之间的边界

**特性模块** 是带有 ` NgModule ` 装饰器及其元数据的类，和根模块一样;两者的 *元数据的属性* 是一样的。

它们共享相同的执行环境：如依赖注入器——意味者某模块定义的服务在所有模块中使用。

不同点：
- **引导** 根模块来 *启动* 应用，但 **导入** 特性模块来 *扩展* 应用。
- 特性模块可以对其他模块暴露或隐藏自己的实现


##### 用路由器实现懒（Lazy）加载

##### 共享模块

##### 核心（Core）模块

##### 清理

##### 用 ` CoreModule.forRoot ` 配置核心服务

##### 禁止多次导入  ` CoreModule `

##### 总结




***
### 依赖注入

***
### HttpClient

***
### 路由与导航

***
### 测试

***
### 速查表

***
## 其他技术













//Angular
