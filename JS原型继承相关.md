### 一. 原型链继承
#### 1. 举例来总结有关prototype的概念
```
var o1 = {}; 
var o2 =new Object();
var o3 = new f1();

function f1(){}; 
var f2 = function(){};
var f3 = new Function('str','console.log(str)');

console.log(typeof Object); //function 
console.log(typeof Function); //function  

console.log(typeof f1); //function 
console.log(typeof f2); //function 
console.log(typeof f3); //function   

console.log(typeof o1); //object 
console.log(typeof o2); //object 
console.log(typeof o3); //object



function Persion(name) {
  this.name = name;
  this.showMe = function() {
    console.log(this.name);
  }
};

var person1 = new Persion('JavaScript');
one.showMe(); // JavaScript
```

+ o1、o2、o3是普通对象，f1、f2、f3是函数对象；f1,f2,归根结底都是通过 new Function()的方式进行创建的。Function Object 也都是通过 New Function() 创建的。

+ Persion 是一个对象
+ person1 是构造函数 Persion 的实例
+ person1.constructor == Person
+ 每个对象都有 __proto _ 属性，但只有函数对象才有 prototype 属性
+ 实例（如persion1）没有 prototype 属性
+ Person.prototype.constructor == Person， 原型对象（Person.prototype）是 构造函数（Person）的一个实例。

+ typeof Person.prototype //Object
+ typeof Function.prototype // Function，这个特殊
+ typeof Object.prototype // Object

+ 当函数本身的属性和方法 与 原型的属性和方法同名时：
	+ 默认调用的是函数对象本身的属性或方法；
	+ 通过原型增加的属性或方法的确是存在的
	+ 函数对象本身的属性或方法的优先级要高于原型的属性或方法

#### 2. [MDN的解释](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes)
+ <font color=red>每个对象拥有一个原型对象，对象以其原型为模板、从原型继承方法和属性。原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为<font color=black>原型链 (prototype chain)</font>，它解释了为何一个对象会拥有定义在其他对象中的属性和方法。</font>

+ 准确地说，这些属性和方法定义在Object的构造器函数(constructor functions)之上的prototype属性上，而非对象实例本身。

+ 在传统的 OOP 中，首先定义“类”，此后创建对象实例时，类中定义的所有属性和方法都被复制到实例中。在 JavaScript 中并不如此复制——而是在对象实例和它的构造器之间建立一个链接（它是__proto__属性，是从构造函数的prototype属性派生的），之后通过上溯原型链，在构造器中找到这些属性和方法。
+ _proto _是每个实例上都有的属性，prototype是构造函数的属性。也就是说，Object.getPrototypeOf(new Foobar())和Foobar.prototype指向着同一个对象。
+ person1.valueOf()该方法调用发生的过程：
	+ 浏览器首先检查，person1 对象是否具有可用的 valueOf() 方法。
	+ 如果没有，则浏览器检查 person1 对象的原型对象（即 Person）是否具有可用的 valueof() 方法。
	+ 如果也没有，则浏览器检查 Person() 构造器的原型对象（即 Object）是否具有可用的 valueOf() 方法。Object 具有这个方法，于是该方法被调用，
	+ **注意：必须重申，原型链中的方法和属性没有被复制到其他对象——它们被访问需要通过前面所说的“原型链”的方式。**

+ 没有官方的方法用于直接访问一个对象的原型对象——原型链中的“连接”被定义在一个内部属性中，在 JavaScript 语言标准中用 [[prototype]] 表示。然而，大多数现代浏览器还是提供了一个名为 __proto__ （前后各有2个下划线）的属性，其包含了对象的原型。你可以尝试输入 person1.__proto__ 和 person1.__proto__.__proto__，看看代码中的原型链是什么样的！
+ Persion.prototype == person1.__proto__   //true
+ Persion.prototype.__proto __ === person1.__proto __.__proto __  //true
+ 原型对象是一个内部对象，应当使用 __proto__ 访问。prototype 属性包含（指向）一个对象，你在这个对象中定义需要被继承的成员。

+ var person2 = Object.create(person1);create() 实际做的是从指定原型对象创建一个新的对象。
	+ person2.__proto__指向Persion
	+ person2.__proto __. __proto __ == Persion.prototype
	+ person2.__proto __结果返回person1对象

+ 每个对象实例都具有 constructor 属性，它指向创建该实例的构造器函数。
+ 获取构造器的名字：person1.constructor.name
+ 基于实例创建实例：
	+ var person3 = Object.create(person2);

#### 3. 缺点：
+ 要想为子类新增属性和方法，必须要在new Animal()这样的语句之后执行，不能放到构造器中
+ 无法实现多继承
+ <font color=red>来自原型对象的引用属性是所有实例共享的</font>
+ <font color=red>创建子类实例时，无法向父类构造函数传参</font>

### 二. 构造继承：[call和apply](https://www.cnblogs.com/mmykdbc/p/6486171.html)
#### 1. 举例：
```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```
#### 2. 缺点

+ 实例并不是父类的实例，只是子类的实例

+ 只能继承父类的实例属性和方法，不能继承原型属性/方法
+ <font color=red>无法实现函数复用，每个子类都有父类实例函数的副本，影响性能</font>

### 三. 实例继承
#### 1. 举例
核心：为父类实例添加新特性，作为子类实例返回

```
function Cat(name){
  var instance = new Animal();
  instance.name = name || 'Tom';
  return instance;
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // false
```
#### 2. 缺点：
+ 实例是父类的实例，不是子类的实例
+ 不支持多继承

### 四. 拷贝继承
#### 1. 举例：
```
function Cat(name){
  var animal = new Animal();
  for(var p in animal){
    Cat.prototype[p] = animal[p];
  }
  Cat.prototype.name = name || 'Tom';
}

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // false
console.log(cat instanceof Cat); // true
```

#### 2. 缺点
+ <font color=red>效率较低，内存占用高（因为要拷贝父类的属性）</font>
+ 无法获取父类不可枚举的方法（不可枚举方法，不能使用for in 访问到）

### 五. 组合继承
核心：通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用

#### 1. 举例：
```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
Cat.prototype = new Animal();

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); // true
```

#### 2. 特点：
+ 弥补了方式2的缺陷，可以继承实例属性/方法，也可以继承原型属性/方法
+ 既是子类的实例，也是父类的实例
+ 不存在引用属性共享问题
+ 可传参
+ 函数可复用

#### 3. 缺点：
+ 调用了两次父类构造函数，生成了两份实例（子类实例将子类原型上的那份屏蔽了）
+ 仅仅多消耗了一点内存

### 六. 寄生组合继承
核心：通过寄生方式，砍掉父类的实例属性，这样，在调用两次父类的构造的时候，就不会初始化两次实例方法/属性，避免的组合继承的缺点
#### 1. 举例：
```
function Cat(name){
  Animal.call(this);
  this.name = name || 'Tom';
}
(function(){
  // 创建一个没有实例方法的类
  var Super = function(){};
  Super.prototype = Animal.prototype;
  //将实例作为子类的原型
  Cat.prototype = new Super();
})();

// Test Code
var cat = new Cat();
console.log(cat.name);
console.log(cat.sleep());
console.log(cat instanceof Animal); // true
console.log(cat instanceof Cat); //true
```
#### 2. 强烈推荐使用，但实现复杂
--------------------------- 

*本文参考较多的文章是：[https://www.cnblogs.com/lurensang/p/6627093.html](https://www.cnblogs.com/lurensang/p/6627093.html)*
