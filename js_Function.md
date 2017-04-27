函数也是对象，是Function类型的实例，也有属性和方法，函数名是指向函数对象的指针，不与某函数绑定。

函数声明语法定义：

			function sum(num1,num2){
				return num1+num2;
			}

函数表达式定义：

			var sum = function(num1,num2){
				return num1+num2;
			};


函数是对象，函数名是指针;

使用不带圆括号的函数名是访问函
数指针，而非调用函数;

			var anothersum = sum;// 将sum值赋值给anothersum，即两者指向同一个函数；
			



Function的arguments属性中大的callee,指向拥有这个 arguments 对象的函数,可以实现松散的耦合；

			function factorial(num){
				if(num<=1){
					return 1;
				}else{
					return num * factorial(num -1);
				}
			}

			function factorial(num){
				if(num<=1){
					return 1;
				}else{
					return arguments.callee(num-1);
				}
			}




注意：使用递归时最好使用第二种方式；


`caller`保存对调用当前函数的函数的引用;

`apply()` 的参数是参数数组(arguments也可以)，参数可以变化
`call()` 函数的参数会直接传递给函数；

		function sum(num1,num2){
			return num1+num2;
		}
		function callSum(num1,num2){
			return sum.call(this,num1,num2);
		}

`callSum()`必须明确的参数的传入；

Function的prototype，ECMAScript引用类型，prototype是保存他们实例方法的真正所在，prototype是不可枚举，不能用`for in `迭代；
每个`funciton`都有2个函数：`apply()` 和 `call()`;用于在特定的作用域中调用函数；


`bind()` 函数：

		var o = { color: "blue" };
		function sayColor(){
			alert(this.color);
		}
		var objectSayColor = sayColor.bind(o);
		objectSayColor(); //blue
		//objectSayColor() = o.sayColor();

