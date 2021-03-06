### 理解对象


### 构建对象的方法
    怎么理解js中的原型链?
    > 每个构造函数都有一个原型对象
    > 每个原型对象都包含一个指向构造函数的指针
    > 每个实例都包含一个指向原型对象的指针
    > 查找方式是一层层向上查找直至顶层Object.prototype

#### 1.工厂模式
```js
  function createObject(name,sex,age,job){
    var o = new Object();
    o.name = name;
    o.sex = sex;
    o.age = age;
    o.job = job;

    return o;
  }

  var person1 = createObject('jack','man',20,'Software Engineer');
```
*缺点没法判断新创建的对象的类型*

#### 2.构造函数模式
```js
 function Person(name,age,job){
   this.name = name;
   this.age = age;
   this.job = job;
   this.sayName = function(){
     alert(this.name);
   }
 }

 var person1 = new Person('jack',20,'Software Engineer');
```
*缺点：声明的方法无法复用，造成内存浪费*

#### 3.原型模式
```js
function Person(){}
Person.prototype.name = 'Jack';
Person.prototype.age = 18;
Person.prototype.job = 'Software Engineer';
Person.prototype.friends = ['Rose','Mike']
Person.prototype.sayName = function (){
  alert(this.name);
}
var person  = new Person();
```
* 1.Object.getPrototypeOf()
* 2.hasOwnProperty()
* 3.in操作符
* 4.hasPrototypeProperty()

*缺点：原型模式的最大问题是共享问题，原型链的属性改变，将影响所有的实例*

#### 4.组合模式
```js
 function Person(name,age,job){
   this.name = name;
   this.age = age;
   this.job = job;
   this.friends = ['Rose','Mike'];
 }
 Person.prototype = {
   construtor:Person,
   sayName:function(){
     alert(this.name);
   }
 }
```

*实例属性都是在构造函数中定义，方法在原型链上继承，目前使用最广泛的方式*

#### 5.动态原型模式
```js
function Person(name,age,job){
  this.name= name;
  this.age = age;
  this.job = job;
  if(typeof  this.sayName != 'function'){
    Person.prototype.sayName = function(){
      alert(this.name);
    }
  }
}
```
*特点：只有在sayName函数没有被创建的时候才会创建*

#### 6.寄生构造函数模式
*寄生构造函数和工厂模式一模一样，除了用new生成实例,返回的对象和构造函数没有关系，不能用instanceof来确定对象类型。拓展对象的特殊方法可以用这种模式，如创建具有特殊方法的数组对象*
```js
function Person(name,age,job){
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function(){
    alert(this.name);
  }
  return o;
}
var person1 = new Person('jack',18,'Software Engineer');
```


#### 7.稳妥构造函数模式
*稳妥对象时指没有公共属性，其方法也不引用this，适合在一些安全环境中使用*
```js
 function Person(name,age,job){
   var o = new Object();
   o.sayName = function(){
     alert(name);
   }
  return o;
 }
```
*这个模式除了sayName方法外没有其他方法可以访问name属性*




### 二、对象继承
    *如何实现继承?*
    原型链继承
    借用构造函数(call,apply)
    组合继承(原型链＋构造函数)
    原型式继承
    寄生式组合式继承

#### 1.原型链继承
*原型链的继承是通过改写子类SubType的prototype实现的，将创建的父类SurperType()的实例，并将实例赋给了子类的SubType的prototype。子类的所有方法在改写prototype之后添加。*
```js
function SurperType(){
  this.property = true;
}
SurperType.prototype.getSurperValue = function(){
  return this.property;
}

function SubType(){
  this.subproperty = false;
}

SubType.prototype = new SurperType();
SubType.prototype.getSubValue = function(){
  return this.subproperty;
}

var instance = new SubType();
instance.getSubValue();
instance.getSuperValue();
```
**注意** *不能用对象字面量创建原型方法，会改写原型。*

**缺点** 
*1.父类成了子类的实例，出现了原型属性共享问题  2.不能向父类的构造函数传参。*

#### 2.借用构造函数继承（经典继承）
*思想：在子类执行父类的构造函数并将父类this指向子类*
```js
function SurperType(name){
  this.name = name;
  this.color = ['red','blue','green'];
}

function SubType(){
  SurperType.call(this,'Jack')
}
var instance1 = new SubType();
instance1.color.push('black');
console.log(instance1.color,instance1.name);

var instance2 = new SubType();
console.log(instance2.color,instance2.name)
```
*问题：仅用构造函数无法解决函数复用的问题*


#### 3.组合继承（伪经典继承）
*指将原型链和构造函数组合起来的继承方式。构造函数实现属性继承，原型链实现方法继承。*
```js
function SurperType(name){
 this.name = name;
}
SurperType.prototype.sayName = function (){
  alert(this.name);
}
function SubType(name,age){
  SurperType.call(this,name);
  this.age = age;
}
SubType.prototype = new SurperType();
SubType.prototype.constructor = SubType;
SubType.prototype.sayAge = function (){
  alert(this.age)
}

var instance = new SubType('Jack',20);
console.log(instance);
instance.sayName();
instance.sayAge();
```
*这种方法综合了两种模式的优点，缺点是调用了两次SurperType构造函数*


#### 4.原型式继承
*思路基于已有的对象原型创建新的对象，不用创建自定义对象。*
```js
  function SurperType(o){
    function F(){};
    F.prototype = o;
    return new F();
  }
```


#### 5.寄生式继承
*思路：和工厂模式思路一样，创建一个封装继承过程的函数。与原型式继承相关紧密相关。*
```js
function object(o){
  function F(){};
  F.prototype = o;
  return new F();
}
function createAnother(origin){
  var clone = object(origin);
  clone.sayHi = function(){
    alert("hi");
  }
  return clone;
}
```
*object()函数不是必须的，任何能够返回对象的函数都适用于此模式*

#### 6.寄生组合式继承
*思路：不必为指定子类的原型而调用父类的构造函数，复制一个弗雷德额副本。*
```js
 function inheritPrototype(subType,surperType){
   var prototype = object(surperType.prototype);
   prototype.constructor = subType;
   subType.prototype = prototype;
 }
```


### 在jQuery中怎么实现原型链继承的?



### *new 操作符做了以下几个事情*
* 1).创建一个新的空对象
* 2).将构造函数的作用域指向新对象，即把this指向新对象call()
* 3).将空对象的__proto指向构造函数的prototype
* 4).返回新对象

```js
  function newFun(obj){
   let o = Object.create({})
       obj.call(o)
       o.__proto__ = obj.prototype
   
   return o
  }
```
