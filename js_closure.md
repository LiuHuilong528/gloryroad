## js 闭包

闭包：指那些能够访问独立(自由)变量的函数 (变量在本地使用，但定义在一个封闭的作用域中)。换句话说，这些函数可以“记忆”它被创建时候的环境。 

也能理解为函数（outer）中有函数（inner），outer函数将inner函数返回，且inner函数中需要访问outer函数中的变量；

如下：

     function outer(){
		var outer_var ='I\'m a outer variable;';
		function inner(){
			console.log(outer_var);
		}
		return inner;
	}
	
	var init = outer();
	init();
     

运行以上代码会在 控制台输出 `I'm a outer variable;`;

这段代码中 `init` 变成了闭包，包含了函数（即inner函数）和创建该函数（inner）的环境。环境则由闭包创建时作用域的这任何局部变量组成。`init`是闭包，由`inner`和`outer_var`变量组成；

下面看个更有趣的实例：

    function add(x){
		return function(y){
			return x+y;
		};
	}
	var add5 = add(5);
	var add10 = add(10)；
	
	console.log(add5(2));// 5+2
	console.log(add10(2)); // 10+2

----------


## 闭包的使用

闭包允许将函数与其所操作的某些数据（环境）关连起来。一般说来，可以使用只有一个方法的对象的地方，都可以使用闭包。

##用闭包模拟私有方法


	var makeCounter = function() {
	  var privateCounter = 0;
	  function changeBy(val) {
	    privateCounter += val;
	  }
	  return {
	    increment: function() {
	      changeBy(1);
	    },
	    decrement: function() {
	      changeBy(-1);
	    },
	    value: function() {
	      return privateCounter;
	    }
	  }  
	};
	
	var Counter1 = makeCounter();
	var Counter2 = makeCounter();
	console.log(Counter1.value()); /* logs 0 */
	Counter1.increment();
	Counter1.increment();
	console.log(Counter1.value()); /* logs 2 */
	Counter1.decrement();
	console.log(Counter1.value()); /* logs 1 */
	console.log(Counter2.value()); /* logs 0 */

`makeCounter`是函数，其的`privateCounter`和`changeBy`函数是`makeCounter`外部无法直接访问，但可以通过返回的三个函数访问（`increment`,`decrement`,`value`）;`Counter1`和`Counter2`是各自独立的；

## 常见错误
每隔 1s 输出一个数字

	function box(){
	    var arr = [];
	    for(var i=0;i<5;i++){
	        arr[i] = function(){
	            return i;
				//由于这个闭包的关系，他是循环完毕之后才返回，最终结果是4++是5
	        }                                        
			//这个匿名函数里面根本没有i这个变量，所以匿名函数会从父级函数中去找i，
	    }                                            
		//当找到这个i的时候，for循环已经循环完毕了，所以最终会返回5
	    return arr;
	}
	console.log(box()[0]());// 5

运行结果是：输出 5；传给 `arr[i]` 的是闭包中的匿名函数，不是闭包对象；，当 `box()` 执行时得到的是 `arr`函数数组，匿名函数返回的是`i`的值,`arr[0]()` 执行时返回 `i`, `arr[0]`函数中没有`i`变量会去父级函数中找到 已经是 5 的 `i`;
