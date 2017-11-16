# ES6Demo
ES6新特性

	//1.let的用法
	//先看如下例子：
	var name0="zach";
	while(true){
		var name0="obama";
		console.log(name0);//obama
		break;
	}
	console.log(name0);//obama
	//使用var两次输出都是obama，这是因为ES5只有全局作用和函数作用域，没有块级作用域。
	//第一种场景就是你现在看的内层变量覆盖外层变量
	//而let咋实际上位JavaScript新增了块级作用域。用它所声明的变量，只在let命令所在的代码块内有效。
	let name1="zach";
	while(true){
		let name="obama";
		console.log(name);//obama
		break;
	}
	console.log(name1);//zach

	//另外一个var带来的不合理场景就是用来计数的循环变量泄漏为全局变量
	var a=[];
	for(var i=0;i<10;i++){
		a[i]=function() {
			console.log(i);
		}
	}
	a[6]();//10

	//上面代码中，变量i是var声明的，在全局范围内都有效。所以每一次循环，新的i值都会覆盖旧值，导致最后输出的是最后一轮i的值。
	//使用let就可以避免这种情况
	var a1=[];
	for (let i=0; i <10; i++){
		a1[i]=function(){
			console.log(i);
		}	
	}
	a1[6]();

	//再来看一个更常见的例子，了解如果不适用ES6，二用闭包如何解决这个问题。
	var clickBoxs=document.querySelectorAll(".clickBoxs");
	for(var i=0;i<clickBoxs.length;i++){
		clickBoxs[i].onclick=function(){
			console.log(i);
		}
	}

	//我们本来希望的是点击不同的clickBox，显示不同的i，但事实上是无论我们点击哪个clickBox，输出的相同的。
	//下面我们来看如何用闭包搞定它。

	function iteratorFactory(i){
		var onclick=function(){
			console.log(i);
		}
		return onclick;
	}
	var clickBoxs=document.querySelectorAll("clickBoxs");
	for(var i=0;i<clickBoxs.length;i++){
		clickBoxs[i].onclick=iteratorFactory(i);
	}

	//2.const的用法
	//const也用来声明变量，但是声明的是常量，一旦声明，常量的值就不能改变

	const PI=Math.PI;
	// PI=23;//报错

	//const有一个很好的应用场景，就是当我们引用第三方库的时候声明的变量，
	//用const来声明可以避免未来不小心重命名而导致出现的bug
	// const moment=require("moment");

	//3.class,extends,super
	//这三个特性设涉及了ES5中最令人头疼的几个部分：原型、构造函数、继承。
	//ES6提供了更接近传统语言的写法，引入了Class的概念。
	class Animal{
		constructor(){
			this.type="animal";
		}

		says(say){
			console.log(this.type+" says "+say);
		}
	}

	let animal=new Animal();
	animal.says("hello");

	class Cat extends Animal{
		constructor(){
			super();
			this.type="cat";
		}
	}

	let cat=new Cat();
	cat.says("hello");

	//上面代码首先用class定义了一个类，可以看到里面有一个constructor方法，就是构造函数，
	//而this关键字则代表实例对象。简单的说constructor内定义的方法和属性是实例对象自己的，
	//而constructor外定义的方法和属性则是所有实例对象可以共享的。
	//Class之间通过extends关键字实现继承，super关键字代指父类的实例，子类必须在构造函数中调用super方法
	//否则新建实例会报错，这点和java差不多。

	//4.arrow function
	function f(i){return i+1;}//ES5
	(i)=>i+1;//ES6

	function f1(x,y){
		x++;
		y--;
		return x+y;
	}

	(x,y)=>{x++;y--;return x+y;}

	//除了看上去更加简洁意外，arrow function还有一项功能
	//长期以来，JavaScript的this对象的指向是一个非诚令人头疼的问题，在对象中使用this必须非常小心。比如：

	class Person{

		constructor(name){
			this.name=name;
		}

		says(say){
			setTimeout(function(){
				console.log(this.name+" says "+say);
			},1000);
		}
	}

	var p=new Person("jack");	
	p.says("hello");//undefined says hello

	//上面代码中setTimeout中的this指向的是全局变量
	//为了解决这个问题，传统的解决办法有两个
	//方法一：
	//将this传给self，在用self来代替this
	//将上面修改为：
	// says(say){
	// 	var self=this;
	// 	setTimeout(function(){
	// 		console.log(self.type+" says "+say);
	// 	},1000);
	// }

	//方法二：
	//使用bind(this)
	// says(say){
	// 	setTimeout(function(){
	// 		console.log(this.type+" syas "+say);
	// 	}.bind(this),1000);
	// }

	//使用箭头函数：
	class Person1{
		constructor(name){
			this.name=name;
		}

		says(say){
			setTimeout(()=>{
				console.log(this.name+" says "+say);
			},1000);
		}
	}
	var p1=new Person1("tom");
	p1.says("hello");//

	//当使用箭头函数时，函数体内的this对象，就是定义时所在的对象，而不是使用时所在对象。
	//并不是因为函数箭头内部有绑定this的机制，实际原因是箭头函数根本没有字的this，它的this是继承外面的，
	//因此内部的this就是外层代码块的this

	//5.template string

	//ES5:
	var basket={count:10,onSale:"apple"}
	var resultDom=document.getElementById("result");
	resultDom.innerHTML="There are <b>"+basket.count+
		"</b> items in your basket,"+
		"<em>"+basket.onSale+
		"</em> are on sale!";

	//ES6
	var resultDom1 = document.getElementById("es6_result");
	resultDom1.innerHTML=`
		Thera are <b>${basket.count}</b> items
		in your basket,<em>${basket.onSale}</em>
		are on sale!
	`;
	//用`来表示起始，用${}来引用变量，而且所有的空格和缩进都会被保留在输出中

	//6.destructuring
	// ES6允许按照一定模式，从数组和对象中提取值，对变量进行复制，这被称为解构（Destructuring）
	//看如下例子：

	let cat1="ken";
	let dog="lili";
	//ES5:
	let zoo={cat:cat1,dog:dog};
	console.log(zoo);
	//ES6:
	zoo={cat1,dog};
	console.log(zoo);
	//反过来
	dog={type:"animal",many:2};
	let {type,many}=dog;
	console.log(type,many);

	//7.default,rest
	//default就是用来指定默认值
	//ES5:
	function animal1(type){
		type=type||"cat";
		console.log(type);
	}
	animal1(type);

	//ES6:
	function animal2(type="cat"){
		console.log(type);
	}
	animal2();

	//rest:
	function animals(...types){
		console.log(types);
	}
	animals("cat","dog","fish");
