# You-Dont-Know-JS
# 作用域与闭包
## 编译器术语

LHS —— 赋值的目标（LHS），取得值赋给变量；   
RHS —— 赋值的源（RHS），单纯的获取变量值。

## 嵌套作用域
变量在直接作用域找不到的话，会去下个外层作用域查找，直到最外层作用域。   

** 遍历嵌套`作用域` 的简单规则 **：引擎 从当前执行的 作用域 开始，在那里查找变量，如果没有找到，就向上走一级继续查找，如此类推。如果到了最外层的全局作用域，那么查找就会停止，无论它是否找到了变量。   
如果作用域中没有变量，在非`strict`模式下，LHS会在全局作用域中创建个变量；在`strict` 则不会出现这样的情况。

## 词法作用域
** 词法作用域 ** ：是在词法分析时被定义的作用域；由变量和作用域的块儿在何处被编写决定的。   
* 作用域查询时，一旦找到第一个匹配，作用域查询就停止。

## 欺骗词法作用域
### `eval`
`eval(...)` 函数接收字符串为参数，字符串内容看成实际编写在程序位置上的JS代码。  
### `with`
`with` -当对象没有 `with` 中的属性时，它会在全局作用域中声明个变量；这样就造成了全局作用域泄露。

这些机制的缺点是，它压制了 引擎 在作用域查询上进行编译期优化的能力，因为 引擎 不得不悲观地假定这样的优化是无效的。这两种特性的结果就是代码 将 会运行的更慢。不要使用它们。

## 函数与块作用域
### 函数中的作用域
函数中的作用域 —— 所有变量属于函数，在整个函数中始终可以使用和重用——甚至在嵌套的作用域中。

### 隐藏于普通作用域
** 隐藏于普通作用域 ** 优点：    
1. 可以实现私有变量或函数；
2. 避免命名冲突；

    ``` javascript
            function foo(){
                funciton bar(a){
                    i = 3;//在外围的for循环中改变 i
                    console.log(a+i);
                }
                for(var i = 0 ; i<10;i++){
                    bar(i*2);//会出现无限循环
                }
            }
            foo();
    ```

3. 全局"命名空间"冲突：例如：jQUery= $ 等等；
4. 模块管理；

### 函数作为作用域

`(function ...)` —— 这里不是函数声明，而是一种函数表达式；在函数表达式后加 `()` ，则函数会** 立即执行 **，成为立即执行函数。   
`(function (){}())` ——立即执行函数的另种形式；    
`(function foo(){...})` 中 `foo`仅可以在 `...` 被找到，不会污染外围作用域

立即执行函数也能仅仅是为了传递参数：

``` javascript
    var a = 2;

    (function IIFE( global ){

        var a = 3;
        console.log( a ); // 3
        console.log( global.a ); // 2

    })( window );

    console.log( a ); // 2

```
传入`window` 并命名成 `global`;这样就不会有命名空间污染了。

立即执行的另一种变种：

``` javascript
    var a = 2;

    (function IIFE( def ){
        def( window );
    })(function def( global ){

        var a = 3;
        console.log( a ); // 3
        console.log( global.a ); // 2

    });

```

`def` 函数在代码后半部分定义,然后作为参数被传给代码前半部分的 `IIFE` 函数。最后参数 `def` (函数)被调用，并将 `window` 作为 `global` 参数传入！

#### 内联函数表达式
内联函数表达式 如下：

``` javascript
        setTimeout(function timeoutHandler(){
            console.log("I waited 1 second");
        },1000);

        timeoutHandler();//这里会报ReferenceError,timeoutHandler只在上函数中的作用域内

```

### 块作用域
JS不支持块作用域；

``` javascript
for (var i=0; i<10; i++) {
	console.log( i );
}

```

上面代码，`i` 会在for循环外的作用域内；

`let` 可以实现块作用域，它总是将变量声明附着在所在的块(通常是`{.....}`)的作用域中。`let` 不会讲变量提升：

``` javascript
{
    console.log(bar);//ReferenceError!
    console.log(foo); // 3 这里是变量提升
    let bar = 2;
    var foo = 3;
}
```

`const` --创建一个块儿作用域变量，但是它的值是固定的（常量）。任何稍后改变它的企图都将导致错误。


## 提升

``` javascript
a = 2;

var a;

console.log( a );//2
```

JS编译器会在编译过程中找到所有的声明（** 变量或函数 **），关联到合适的作用域上；这些声明会首先被处理；    
上述代码被处理成如下：    

``` javascript
/**编译处理**/
var a;         //编译器将声明提升 - 第一阶段
a=2;           // 执行 -- 第二阶段，赋值不会被编译器处理的还在原处
console.log(a);//2
```

``` javascript
console.log( a );

var a = 2;

/** 编译器处理成如下 **/
var a ;         // 编译器变量提升
console.log(a); // 不变-无声明
a = 2;          // 不变-无声明
```

** 注意 ** 函数声明会被提升，而函数表达式不会被提升。
### 提升中-函数优先
*   当函数声明和变量声明的名称相同时，函数会首先提升，而后是变量。多个重复的 `var `声明，后面的会覆盖前面的。

``` javascript
foo(); // 1

var foo;

function foo() {
	console.log( 1 );
}

foo = function() {
	console.log( 2 );
};

/** 引擎解释如下 **/
function foo() {
	console.log( 1 );
}

foo(); // 1

foo = function() {
	console.log( 2 );
};
```
## 作用域闭包
**闭包** 就是函数能够记住并访问它的词法作用域，即使当这个函数在它的词法作用域之外执行时。

* 内部函数只要被**传递到词法作用域外**，它都将维护一个指向它最开始被声明时的作用域的引用，一旦被执行，闭包就会被行使。

 ### 模块化

 ``` javascript
 /** 模块化的JS **/
function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
}

var foo = CoolModule();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
 ```

 行使模块2个必要条件：
 1. 要有外部的外围函数，且至少被调用一次；
 2. 外围函数至少返回一个内部函数，这样才可以访问私有作用域的变量；

 单利模块；
 ``` javascript
 var foo = (function CoolModule() {
	var something = "cool";
	var another = [1, 2, 3];

	function doSomething() {
		console.log( something );
	}

	function doAnother() {
		console.log( another.join( " ! " ) );
	}

	return {
		doSomething: doSomething,
		doAnother: doAnother
	};
})();

foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3

// 这里*立即执行*只能这使用，不能用 `CoolModule()`
 ```
 ## 附录A 动态作用域
 **动态作用域**不关心函数和作用域在哪里和如何被声明，而是关心**它们是从何处被调用的**。    
 JavaScript**实际上没有动态作用域**。有词法作用域，但是 `this` 机制和动态作用域类似。

 ``` javascript
 function foo() {
	console.log( a ); // 3  (不是 2!)
}

function bar() {
	var a = 3;
	foo();
}

var a = 2;

bar();//
/** 运行结果 **/
2           //如果是动态作用域则此是 3 ，说明js无动态作用域
undefined   
 ```


 词法作用域和动态作用域差异：**词法作用域是编写时的，而动态作用域（和 `this`）是运行时的**。词法作用域关心的是 函数在何处被声明，但是动态作用域关心的是函数 从何处 被调用。

 # `this` 与对象原型

 ## `this`

 1. 函数中的this 并不是指向函数自己。    

    ``` javascript
    function foo(num) {
        console.log( "foo: " + num );

        // 追踪 `foo` 被调用了多少次
        this.count++; //
    }

    foo.count = 0;//foo函数中的count 并没有绑定到foo.count 上，函数中的this不是指向函数自身

    var i;

    for (i=0; i<10; i++) {
        if (i > 5) {
            foo( i );
        }
    }
    // foo: 6
    // foo: 7
    // foo: 8
    // foo: 9

    // `foo` 被调用了多少次？
    console.log( foo.count ); // 0 -- 这他妈怎么回事……？

    /*** 在函数引用函数自身 回避了this ****/

    function foo() {
        foo.count = 4; // `foo` 引用它自己
    }

    ```

    正确的做法是强制`this`指向han'函数对象：     

    ``` javascript     
    function foo(num){
        console.log("foo"+num);
        this.count++;
    }
    foo.count = 0;//函数对象的属性赋值
    for(var i=0;i<5;i++){
        // `call(...)` 保证了函数中的this 指向函数对象；
        foo.call(foo,i);
    }
    console.log(foo.count);
    ```
 2. `this` 不知道指向函数的作用域。    
    `this` 不会指向词法作用域，`this` 不能隐含的引用函数的词法作用域；

 ### this
 `this` 不是编写时绑定，而是运行时绑定。依赖于函数调用的上下文，与函数声明位置没有任何关系，而与函数的调用方式紧密联系；     

 当函数调用时，会建立一个执行环境的活动记录。记录包括是从何处（调用栈——call-stack）被调用的，函数是如何被调用的，被传递了什么参数等的信息。记录的属性之一，在函数执行期间被使用`this`的引用。


 ## `this` 解析
* **调用点**——函数被调用的地方。     
  **调用栈**——达到当前执行位置而被调用的所有方法的堆栈。

``` javascript
function baz() {
    // 调用栈是: `baz`
    // 我们的调用点是 global scope（全局作用域）

    console.log( "baz" );
    bar(); // <-- `bar` 的调用点
}

function bar() {
    // 调用栈是: `baz` -> `bar`
    // 我们的调用点位于 `baz`

    console.log( "bar" );
    foo(); // <-- `foo` 的 call-site
}

function foo() {
    // 调用栈是: `baz` -> `bar` -> `foo`
    // 我们的调用点位于 `bar`

    console.log( "foo" );
}

baz(); // <-- `baz` 的调用点
```

### `this` 绑定四原则：
- 默认绑定;
  - 独立函数的调用 - `this`绑定到全局对象
- 隐含绑定-调用点是否有一个环境对象（context object），也称为拥有者（owning）或容器（containing）对象;
  ``` javascript
  function foo() {
  	console.log( this.a );
  }

  var obj = {
  	a: 2,
  	foo: foo
  };

  obj.foo(); // 2
  ```
  **注意** `foo()` 被声明然后作为引用属性添加到 `obj` 上的方式。无论 `foo()` 是否一开始就在 `obj` 上被声明，还是后来作为引用添加（如上面代码所示），这个函数都不被 `obj` 所真正“拥有”或“包含”。    
  **隐含丢失**
  ```` javascript
  function foo() {
  	console.log( this.a );
  }

  var obj = {
  	a: 2,
  	foo: foo
  };

  var bar = obj.foo; // 函数引用！

  var a = "oops, global"; // `a` 也是一个全局对象的属性

  bar(); // "oops, global"
  ```
- 明确绑定-借用 `call(...)` 和 `apply(...)`         
  硬绑定将函数用`call(...)`或者`apply(...)`包装一层
  ``` javascript
  // foo() 的this 会根据 调用的情况而变化
  function foo() {
  	console.log( this.a );
  }

  var obj = {
  	a: 2
  };

  var bar = function() {
  	foo.call( obj );
  };
  // bar 的 `this` 已经固定为 obj 且不可以改变
  bar(); // 2
  setTimeout( bar, 100 ); // 2

  // `bar` 将 `foo` 的 `this` 硬绑定到 `obj`
  // 所以它不可以被覆盖
  bar.call( window ); // 2
  ```
  ES6内建工具绑定`Function.prototype.bind`
- `new` 绑定    
  在函数前 `new` 时，构造器调用时，完成如下事情：
  - 全新的对象被构建
  - 新构建的对象被接入原型链
  - 新构建的对象被设置位函数调用的 `this` 的绑定;
  - 除非函数返回它自己的其他对象，否则这个`new`函数返回自动构建的对象;
  ``` javascript
  function foo(a){
    this.a = a;
  }
  var bar = new foo(2);
  console.log(bar.a);
  ```

**`this`绑定原则优先级**(低到高)    
>1.默认 2.隐含绑定 3.`new` 4.硬绑定
### 判定`this`
判定`this`按照如下顺序：
1. 函数是通过`new`调用的吗？ 是则`this` 是新构建的对象;
2. 函数是否通过call和apply被调用，甚至隐藏在 bind 硬绑定之中？是，则this就是那个被绑定的对象。
3. 函数是通过环境对象被调用？是则，this就是那个环境对象;
4. 否则this绑定在global对象;

### 绑定的特例
- 被忽略的`this`    
  将null、undefined 作为 call apply或bind的this的绑定参数，这些值会被忽略，适用默认绑定规则
- 间接引用
- 软化绑定

### 词法`this`
箭头函数通过“大箭头”操作符：`=>`与使用四种标准的 `this`不同，箭头函数从封闭它的作用域采用this绑定;
```javascript
function foo() {
  // 返回一个箭头函数 -被函数 foo（）封闭 这里的a就是 调用foo()的对象的a
	return (a) => {
    // 这里的 `this` 是词法上从 `foo()` 采用的
		console.log( this.a );
	};
}

var obj1 = {
	a: 2
};

var obj2 = {
	a: 3
};

var bar = foo.call( obj1 );
bar.call( obj2 ); // 2, 不是3!
```  

## 对象
* 函数不会属于对象;尽管声明函数表达式作为字面对象的一部分，函数也不会属于这个对象——只是同个对象多次引用。

### 属性描述符

* 四个属性描述符：
  1. `writable`
  2. `enumerable`
  3. `configurable`
  4. `value`

`object.getOwnPropertyDescriptor(object,property)` 查看属性的描述符;    
`Object.defineProperty(object,property,{descriptorConfigKey:Value})` 添加或修改描述符的值;

``` javascript
var myObject = {};

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: true,
	configurable: true,
	enumerable: true
} );

myObject.a; // 2
```



* `writable`控制改变属性值的能力;值是：`false`时对属性值的修改会失败！

  ``` javascript
    var myObject = {};

    Object.defineProperty( myObject, "a", {
    	value: 2,
    	writable: false, // 不可写！
    	configurable: true,
    	enumerable: true
    } );

    myObject.a = 3;

    myObject.a; // 2 修改失败
  ```

* 可配置性`configurable` - 控制其他三个描述符的值是否可以修改
  - `configurable` 为`false`是单向操作，不可以撤销———`true`可以设置为`false`，`false`不能再设置`true`
  - `configurable:false`时，则`writable`只能从`true`变`false`;
  - `configurable:false`时，`delete` 操作符移除既存属性的能力;
* 可枚举性`enumerable`-- 控制着一个属性是否能在特定的对象-属性枚举操作中出现，如：`for ... in`
  - `enumerable:false` 时属性可以正常访问，但是不能在枚举中出现的;
* 不可变`Immutable`

四种方法创建不可变对象：    
  1. `writable:false`与`configurable:flase` 组合创建常量;
  2. 防止扩展(Prevent Extensions)——`Object.preventExtensions(obj)`
  3. 封印(Seal)    
     `Objecet.seal(obj)` 意味在当前对象调用`Objecet.preventExtensions(...)`,同时将既有属性设置`configurable:false`    
     不能添加新属性，不可以重新配置和删除既有属性——但是可以修改值
  4. 冻结(Freeze)    
     `Objecet.Freeze(obj)` 创建冻结对象，意味在当前对象调用`Object.seal(obj)`,同时设置`writable:false` ,因此值不可变;      
     这样不能用于引用对象的内容，“深度冻结”对象：在对象上调用`Object.freeze(...)`,然后在属性引用对象迭代递归此方法;



* [[GET]] 操作——获取属性值
* [[PUT]] 操作——设置或创建并赋值

### Getters 与 Setters
为属性定义拥有 getters和setters或两者兼备，则定义就成了“访问器描述符”。访问器描述符，它的`value` 和 `writable`性质没有意义被忽略，JS会考虑的属性有 `get` `set` `configurable` `enumerable`;

``` javascript
var myObject = {
	// 为 `a` 定义一个 getter
	get a() {
		return 2;
	}
};

Object.defineProperty(
	myObject,	// 目标对象
	"b",		// 属性名
	{			// 描述符
		// 为 `b` 定义 getter
		get: function(){ return this.a * 2 },

		// 确保 `b` 作为对象属性出现
		enumerable: true
	}
);

myObject.a; // 2

myObject.b; // 4
```

### 存在性（Existence）
`in`操作符——属性是否存在于对象中，亦或存在于`[[prototype]]`链对象遍历的更高层中;`hasOwnProperty(...)`**仅仅**检查是否拥有属性，不会查询`[[prototype]]`链。
#### 枚举(Enumeration)
`Object.propertyIsEnumerable(...)`判断属性是否直接存在于对象上，并且`enumerable:true`.    
'Object.keys(...)' 返回对象**所有可枚举**属性的数组;`Object.getOwnPropertyNames(...)`返回所有属性的数组，**不论是否可枚举**。
### 迭代(Iteration)
- `forEach(...)` 迭代数组中所有的值，忽略回调的返回值。
- `every(...)` 迭代数组到最后，或者当回调返回 `false` 值时停止;
- `some(...)` 迭代到最后，或者回调返回 `true` 值时停止;
- ES6 `for .. of` 迭代可以直接获得迭代值：

  ``` javascript
    var  myArray = [1,2,3];
    for(var v of myArray){
      conole.log(v);
    }
    //1
    //2
    //3
  ```

  `for ... of` 循环会提供一个迭代器;`@@iterator` 内部对象;

  ``` javascript
    var myArray = [1,2,3];
    var it = myArray[Symbol.iterator]();
    it.next();//{value:1,done:false}
    it.next();//{value:2,done:false}
    it.next();//{value:3,done:false}
    it.next()://{done:true}
  ```

## 混合（淆）“类”的对象
类是一种设计模式。许多语言提供语法来启用自然而然的面向类的软件设计。JS也有相似的语法，但是行为和其他语言的工作原理**有很大不同**。

**类意味着拷贝。**

当一个传统的类被实例化时，就发生了类的行为向实例中拷贝。当类被继承时，也发生父类的行为向子类的拷贝。

多态（在继承链的不同层级上拥有同名的不同函数）也许看起来意味着一个从子类回到父类的相对引用链接，但是它仍然只是拷贝行的的结果。

JavaScript**不会自动地**（像类那样）在对象间创建拷贝。


mixin 模式常用于在*某种程度上*模拟类的拷贝行为，但是这通常导致像显式假想多态那样（`OtherObj.methodName.call(this, ...)`）难看而且脆弱的语法，这样的语法又常导致更难懂和更难维护的代码。

明确的 mixin 和类*拷贝*又不完全相同，因为对象（和函数！）仅仅是共享的引用被复制，不是对象/函数自身被复制。不注意这样的微小之处通常是各种陷阱的根源。

一般来讲，在 JS 中模拟类通常会比解决当前*真正*的问题埋下更多的坑。

## 原型
### [[Prototype]]
[[Prototype]] ——一个其他对象的引用的JS对象的内部属性；对象被创建时，被赋予`null`.

**`Object.prototype`**

每个*普通*`[Prototype]`链的最顶端，就是内建`Object.prototype`.

属性值的查找总是从`[Prototype]`的**最低层开始找**；

`myObject.foo="bar"` ，`foo`不直接存在于`myObject`,而在`myObject`的`[[Prototype]]`链的高层；三种场景：      
- 属性的`writable:true`，则直接在`myObject`上添加新属性，形成**遮蔽属性**；
- 属性的`writable:fasle`,则**赋值会报错**，不会有遮蔽属性；
- 属性是个`setter`，则 `setter`被调用，`myOjbect`不会有新属性，也不会有遮蔽属性；

### “类”
**JS中没有类，仅有对象。**

#### “类”函数

JS中所有的函数默认会得到一个公有、不可枚举的属性——`prototype`——可以指向任何的对象，称为 原型。

``` javascript
  function Foo(){

  }
  var a = new Foo();
  Object.getPrototypeOf( a ) === Foo.prototype; // true
```

在以上这段代码中，`a`的`[[Prototype]]`和`Foo.prototype`所指的对象是同一个对象；

**“构造器”(Constructors)**

``` javascript
function Foo() {
	// ...
}

Foo.prototype.constructor === Foo; // true

var a = new Foo();
a.constructor === Foo; // true
```

- 在`Foo.prototype`上的`.constructor`属性仅当`Foo`函数被声明时才出现在对象上。如果你创建一个新对象，并用它替换函数默认的`.prototype`对象引用，这个新对象上将不会魔法般地得到`.contructor`。

```javascript
function Foo() { /* .. */ }

Foo.prototype = { /* .. */ }; // 创建一个新的prototype对象

var a1 = new Foo();
a1.constructor === Foo; // false!
a1.constructor === Object; // true!
```

上面代码的解释：`a1`没有`.constructor`属性，所以它沿者`[[Prototype]]`链向上委托到了`Foo.prototype`。但是这个对象也没有`.constructor`（默认的`Foo.prototype`对象就会有！），所以它继续委托，这次轮到了`Object.prototype`，委托链的最顶端。那个 对象上确实拥有`.constructor`，它指向内建的`Object(..)`函数。

- `Object.create(...)`创建新对象，并将对象链接到指定对象上，`Bar.prototype = Object.create( Foo.prototype )`;
- `._proto_`属性设置，可以改变对象的链接；而ES6中`Object.setPrototypeOf(...)`改变对象的链接；

- `instanceof` 在对象的整个`[[Prototype]]`中，有没有函数的`.prototype`指向的对象；
- `._proto_` 获得了`[[Prototype]]`对象的引用,ES6标准化了；是 getter/setter ：

``` javascript
Object.defineProperty( Object.prototype, "__proto__", {
	get: function() {
		return Object.getPrototypeOf( this );
	},
	set: function(o) {
		// setPrototypeOf(..) as of ES6
		Object.setPrototypeOf( this, o );
		return o;
	}
} );
```

- 使两个对象相互链接在一起的最常见方法是将`new` 关键字与函数调用一起使用     
  使用`new`调用的函数有个被随便的命名为 `.prototype` 的属性，这个属性引用的对象恰好就是新对象链接到的“另一个对象”。带有`new` 的函数调用通常被称为“构造器”，尽管实际上没有传统的面向对象语言那样初始化一个类。

- javascript 中的这些机制和传统的面向对象语言的“初始化类”和“类继承” 类似，关键区别是：javascript中没有方法的拷贝发生，而是通过对象最终通过`[[Proprotype]]`链接在一起。    
  **Javascript中的面向对象的特性，"委托"是个更确切的术语，这些关系不是_拷贝_而是委托链接**



### 行为委托

- 行为委托：在某个对象的属性或方法没能在对象上找到时，让这个对象为属性或方法引用到一个委托。     
  **注意：**委托更适合作为内部实现的细节，不可以直接暴露在API接口设计中。

- **OLOO**（链接到其他对象的对象）是一种没有类的抽象，而直接创建和关联对象的代码风格。OLOO十分自然地实现了基于`[[Prototype]]`的行为委托。

### 附录A: ES6 `class`
 `class`在假装修复JS中的类/继承设计模式的问题上做的好。实际上上:**它隐藏了许多问题，而且引入其他微妙而危险的东西**。


## 类型与文法
### 类型

#### 内建类型：
- `null`
- `undefined`
- `boolean`
- `number`
- `string`
- `object`
- `symbol`

**注意**：除了`object`其他都是**“基本类型”(primitives)**.

`typeof`检测值的类型，但是返回值并不是一一对应的：

``` javascript
typeof undefined        === 'undefined';//true
typeof true             === 'boolean';//true
typeof 42               === 'number';//true
typeof "45"             === 'string';//true
typeof {'life':7777}    === 'object';//true

//ES6 新类型
typeop Symbol()         === 'symbol';//true

typeof null             === 'object';//true

typeof function a(){}   === 'function';//true
```

`typeof null` **返回值是** `object`    
`function` 是对象（object）的子类型，拥有 `[[call]]` 内部属性，就可以 被调用的对象；



#### 值作为类型

JavaScript中变量没有类型---**值才有类型**。


**`undefined` vs `undeclared`**
- `undefined` 声明了变量，但是还没有赋值
- `undeclared` 作用域中没有声明；


### 值

Javascript的`string`是 不可变得，而`array`是相当可变的；**`string`上没有一个方法可以原地修改它的内容**，而总是创建并返回新的`string`，`array` 内容的方法实际上是原地修改。

**机械极小值**——JS `number` 的误差允许范围；`2^-52(2.220446049250313e-16)`;

#### 不是值得值
`undefined` 有且只有一个值： `undefined`     
`null` 有且只有一个值： `null`    
`NaN` 不会等于自己，不具有反射性的值。

JS中和java一样是引用传递参数；

### 原生类型
`Function.prototype`是个函数，`RegExp.prototype`是个正则表达式，而`Array.prototype`是个数组。


### 强制转换
#### ToString
**JSON字符串化**

`JSON.stringfy(...)`将值序列化为JSON兼容的`string`的值。

``` javascript
JSON.stringify( 42 );	// "42"
JSON.stringify( "42" );	// ""42"" （一个包含双引号的字符串）
JSON.stringify( null );	// "null"
JSON.stringify( true );	// "true"
```

`JSON.stringify(..)`工具在遇到`undefined`，`function`，和`symbol`时将会自动地忽略它们。如果在一个`array`中遇到这样的值，它会被替换为`null`（这样数组的位置信息就不会改变）。如果在一个`object`的属性中遇到这样的值，这个属性会被简单地剔除掉。

``` javascript
JSON.stringify( undefined );					/*undefined*/
JSON.stringify( function(){} );					/*undefined*/

JSON.stringify( [1,undefined,function(){},4] );	/**"[1,null,null,4]"**/
JSON.stringify( { a:2, b:function(){} } );		// "{"a":2}"
```

`JSON.stringify(...)`字符串化带有循环引用的`Object`会抛出错误；可以在Object上定义`toJOSN()`方法来处理；JSON字符串化会首先调用Object上的`toJSON`方法，以取得序列化的值;`toJSON()`-变为一个适用于字符串化的JSON安全值。

``` javascript
var o = { };

var a = {
	b: 42,
	c: o,
	d: function(){}
};

// 在`a`内部制造一个循环引用
o.e = a;

// 这回因循环引用而抛出一个错误
// JSON.stringify( a );

// 自定义一个JSON值序列化
a.toJSON = function() {
	// 序列化仅包含属性`b`
	return { b: this.b };
};

JSON.stringify( a ); // "{"b":42}"

```


`JSON.stringify(..)`的第二个参数是可选的，可以使 `Array` 也可以是 `function` ，过滤掉属性


#### ToBoolean
* Falsy值列表：
  - `undefined`
  - `null`
  - `false`
  - +0 -0 NaN
  - ""


#### 明确的强制转换

**String <---> Numbers**

``` javascript
var a = '3.14';
var b = + c ;// b = 3.14 一元操作符将操作数强制转化为  Number
```

一元操作符 `-` `*` `\` 都可以将`String`转换成 `number`，这些操作仅为数字定义；

`||` 和 `&&` 操作符都在 **第一个操作数** 上进行 `boolean` 测试；如果第一个操作数不是 `boolean` 则进行 `ToBoolean` 转换；   
`||` 如果测试结果是 `true` ，第一个操作数作为结果；结果为 `false` 则将第二个操作数作为结果；`&&` 结果是 `true` ，第二个操作数作为结果；结果为 `false` 则将第一个操作数作为结果。

``` javascript
  var a = 42;
  var b = "abc";
  var c = null;

  a || b;		// 42
  a && b;		// "abc"

  c || b;		// "abc"
  c && b;		// null
```

“ `==` 允许在等价性比较中进行强制转换，而 `===` 不允许强制转换”。

当 `==` 的任意一边是一个boolean值时， `boolean` 总是首先被强制转换为一个 `number` 。

`null == undefined` 返回 `true`;


### 文法
#### 表达式副作用

``` javascript
var a = 1;
var b = a++;
// b=1 ; a =2
```


#### 上下文规则
#### 操作符优先级

```javascript
a && b || c  ? c || b ? a : c && b : a
//等同于如下 因为 && > || > ?: 优先级高
（a && b || c ）?(c || b ) ? a : (c && b) : a
```

##### `try .. finally`

``` javascript
function foo() {
	try {
		return 42;
	}
	finally {
		console.log( "Hello" );
	}

	console.log( "never runs" );
}

console.log( foo() );
// Hello
// 42
```

`return 42` 立即运行，设置好`foo()`函数返回值。这些完成了`try`子句而`finally` 子句运行。如此 `foo()` 函数才完成；

``` javascript
function foo() {
	try {
		return 42;
	}
	finally {
		// 这里没有 `return ..`，所以返回值不会被覆盖
	}
}

function bar() {
	try {
		return 42;
	}
	finally {
		// 覆盖前面的 `return 42`
		return;
	}
}

function baz() {
	try {
		return 42;
	}
	finally {
		// 覆盖前面的 `return 42`
		return "Hello";
	}
}

foo();	// 42
bar();	// undefined
baz();	// "Hello"
```


## 异步与性能
### 异步：现在与稍后
事件运行时，事件轮询将运行至队列为空。事件轮询的每次迭代称为一个“tick”。用户交互，IO,和定时器将事件在事件队列中排队。

### 回调

### Promisses
#### 链式流程
Promise两个固有行为：
* `Promise` 上调用 `then(...)` 时，返回的是个新的 `Promise` ；    
* `then(...)` 返回的值都可以被Promise链接

**Resolve(解析),Fultill(完成),Reject(拒绝)**

###　Generator
generator的声明格式化二中形式：

``` javascript
//形势1
function* funName(){}
// 形式2
function *funName(){}

//example
var x = 1;
function *foo(){
  x++;
  yield;//暂定
  console.log("x:",x);
}
//构建迭代器 it 控制 Generator
var it = foo();
// 开始 foo()
it.next();
x;              //2
bar();  
x;              //3
it.next();      // x:3

```

##### 迭代通信

``` javascript
function *foo(x){
  var y = x * (yield);
  return y;
}

var it = foo(6);

it.next(); // 启动 foo() foo会在 yield 暂停；也就是在赋值语句处

var res = it.next(7);//此时 将 7 做为 yield 表达式值传回去； var y = 6 * 7
res.value = 42;

//  example2
function *foo(x){
  var y = x * (yield 'Hello');    // <-- 让出一个值,做为next的返回值
  return y;
}
var it = foo(6);

var res = it.next();              // 第一次 next()
res.value ;                       // "Hello"

res = it.next(7);                 //  传递 7 给 yield
res.value;                        // 42
```

在上例中 `yield` 和 `next()` 一起在 generator 运行期间 构成双向消息传递系统。































3
