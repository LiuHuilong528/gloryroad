# You-Dont-Know-JS
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
** 闭包 ** 就是函数能够记住并访问它的词法作用域，即使当这个函数在它的词法作用域之外执行时。