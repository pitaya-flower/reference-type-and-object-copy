# reference-type-and-object-copy
引用类型及对象拷贝
# 引用类型与非引用类型
```
ECMA Script中规定，javascript的基本数据类型分为两类，即基本数据类型和引用类型,其中基本数据类型包括5样,number、string 、boolean、以及undefined、null两个特殊类型,其次引用类型包括狭义的Object、Array、Function、正则等.

关于这两种数据类型，他们的不同之处在于数据的保存的形式不同，对于基本类型的数据，其保存的方式是在内存中的栈空间，开辟一个固定大小的空间进行固定位置的保存，并且数据的访问形式是后进先出，而引用类型的数据，其保存的形式是在内存的堆空间开辟一个动态的空间保存来保存数据的值(因为数据的大小不固定)，当我们访问这个变量的时候其实是根据出存在栈空间的指针寻址来查找到相应的堆内存空间，这也就很好地解释了为什么，当我们给基本类型进行重复赋值的时候，基本数据的值不会改变，而引用类型会一起连带改变，因为我们的改变不再是单独去创建一个副本，而是查找了同一个指针引用并改变了其堆内存中的值 。

(string类型有些特殊，因为字符串具有可变的大小，所以显然它不能被直接存储在具有固定大小的变量中。由于效率的原因，我们希望JS只复制对字符串的引用，而不是字符串的内容。但是另一方面，字符串在许多方面都和基本类型的表现相似，而字符串是不可变的这一事实（即没法改变一个字符串值的内容），因此可以将字符串看成行为与基本类型相似的不可变引用类型。
)
```
#如下代码输出结果及原因
```
var obj1 = {a:1, b:2};
var obj2 = {a:1, b:2};
console.log(obj1 == obj2);//两个不同的对象储存的是不同的指针。false
console.log(obj1 = obj2);//将obj2赋值给obj1,就是对指针进行复制,那么两者的指针指向同一个堆内存空间.
console.log(obj1 == obj2);//两个对象的指针是相同的，指向同一个内存空间。ture
``` 
#如下代码输出结果及原因
```
var a = 1
var b = 2
var c = { name: '饥人谷', age: 2 }
var d = [a, b, c]

var aa = a
var bb = b
var cc = c
var dd = d

a = 11
b = 22
c.name = 'hello'//改变c.name的值
d[2]['age'] = 3  //改变c.age的值

console.log(aa) //会输出1,即便后面a又重新赋值成11但是number做为基本数据类型，其赋值后，只是相当于拷贝了一个值的副本并重新创建了一个栈内存空间在原来的a之上(或后).
console.log(bb) //会输出2,同上
console.log(cc)//会输出{ name: 'hello', age: 3 }
console.log(dd)//会输出[ 1, 2, { name: 'hello', age: 3 } ]
cc和dd都是引用类型，变量保存的是堆内存地址（相当于保存遥远内存区段的地址），改变对象的属性值只是改变对象指向的内存区段里的数据，所以对象保存地址不变，输出仍是地址指向的内存区段里面的数据；这时由于name和age两个属性值变化，所以输出内容有所变化。
```
#如下代码输出结果及原因
```
var a = 1
var c = { name: 'jirengu', age: 2 }

function f1(n){
  ++n
}
function f2(obj){
  ++obj.age
}

f1(a)  //1 ，会默认执行赋值动作，var n = a,属于值传递，++n时，a依旧保持原始值不变的
f2(c)  //会默认执行var obj = c;这时候它们都是指向同一块堆内存，当执行++obj.age时,对象c的age属性加一，所以这个时候c = {name:'jirengu',age:3}
f1(c.age) //取出c.age = 3 默认执行var n = 3.不会影响c.age
console.log(a) //会输出1
console.log(c)//会输出{ name: 'jirengu', age: 3 }
```
# 过滤如下数组，只保留正数，直接在原数组上操作
```
var arr = [3,1,0,-1,-3,2,-5]
function filter(arr){
	for (var i=0; i<arr.length; i++) {
		if (arr[i] <= 0) {
			arr.splice(i,1); //当有一个数被删除时，下一个数的index就变成了现在的这个值。如果继续循环就会错过下一个元素
			i--;
		}
	}
}
filter(arr)
console.log(arr) // [3,1,2]
```
#过滤如下数组，只保留正数，原数组不变，生成新数组
```
var arr = [3,1,0,-1,-3,2,-5]
function filter(arr){
var newArr = []; 
var j = 0;
	for (var i=0; i<arr.length; i++) {
		if (arr[i] > 0 ) {
			newArr[j] = arr[i];
			j++;
		}
	}
	return newArr;
}
var arr2 = filter(arr)
console.log(arr2)
console.log(arr)
```
# 写一个深拷贝函数，用两种方式实现.
```
深拷贝指的是，当我们重新给引用类型赋值到一个新的变量的时候，其所赋值的内容不再是简单的将地址进行拷贝，而是另外创建了一个栈空间，并用于保存新值的地址,这样当我们去改变原始引用地址的属性值时，并不会影响后续新赋值的引用类型的值,其核心思想是使用递归，并递归至基本类型变量后，再复制。
```
浅拷贝示例：
```
var obj = {
	name:'abc',
	friend:{
		name:'def',
		sex:'female'
	}
}
function shadowCopy(obj) {
	var newObj = {};
	for (var key in obj) {
	   	//每一项直接赋值，如果是引用类型就会出问题
		newObj[key] = obj[key];
	}
   return newObj;
}
var obj2 = shadowCopy(obj);
obj2.name = 'ghi';
console.log(obj);
obj2.friend.name = 'jkl';
console.log(obj);
//改变非引用类型的时，原对象不会发生变化。但是如果有引用类型时就会影响，所以需要深拷贝
```
深拷贝示例1
```
function deepCopy(obj) {
	var newObj = {};
	for (var key in obj) {
		if (typeof obj[key] === 'object' || typeof obj[key] !=== 'null' ) 
                {
                       newObj[key] = deepCopy[obj[key]];	
                }
                else {
			newObj[key] = obj[key];	
		}
	}	
	return newObj;
}
```
深拷贝示例2
```
//利用json.stringify()将对象转换为字符串，然后再利用json.parse()将字符串转换为对象
function deepCopy1(obj) {
	return newObj = JSON.parse(JSON.stringify(obj));
}
```
