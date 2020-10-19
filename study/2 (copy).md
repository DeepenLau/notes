1. ```js
   function fun(){
       this.a=0;
       this.b=function(){
           alert(this.a);
       }
   }
   fun.prototype={
       b:function(){
           this.a=20;
           alert(this.a);
       },
       c:function(){
           this.a=30;
           alert(this.a)
       }
   }
   var my_fun=new fun();
   my_fun.b(); // '0'
   my_fun.c(); // '20'
   
   
```
   
2. ```js
   function C1(name) {
       if (name) {
           this.name = name;
       }
   }
   function C2(name) {
       this.name = name;
   }
   function C3(name) {
       this.name = name || 'join';
   }
   C1.prototype.name = 'Tom';
   C2.prototype.name = 'Tom';
   C3.prototype.name = 'Tom';
   alert((new C1().name) + (new C2().name) + (new C3().name));
   // 'Tomundefinedjoin'
   ```

3. ```js
   function Fn() {
       let a = 1;
       this.a = a;
   }
   Fn.prototype.say = function () {
       this.a = 2;
   }
   Fn.prototype = new Fn;
   let f1 = new Fn;
   
   Fn.prototype.b = function () {
       this.a = 3;
   };
   console.log(f1.a); // 1
   console.log(f1.prototype); // undefined
   console.log(f1.b); // 函数
   console.log(f1.hasOwnProperty('b')); // false
   console.log('b' in f1); // true
   console.log(f1.constructor == Fn); // true
   ```

4. ```js
   function Foo() {
       getName = function () {
           console.log(1);
       };
       return this;
   }
   Foo.getName = function () {
       console.log(2);
   };
   Foo.prototype.getName = function () {
       console.log(3);
   };
   var getName = function () {
       console.log(4);
   };
   function getName() {
       console.log(5);
   }
   Foo.getName();          // 2
   getName();              // 4
   Foo().getName();        // 1
   getName();              // 1
   new Foo.getName();      // 2
   new Foo().getName();    // 3
   new new Foo().getName();// 3 / 返回实例
   ```

5. ```js
   
   Number.prototype.plus = function (num) {
     return this + num
   }
   Number.prototype.minus = function (num) {
     return this - num
   }
   
   let n = 10;
   let m = n.plus(10).minus(5);
   console.log(m);//=>15（10+10-5）
   
   ```

6. ```js
   var a = ?;
   if (a == 1 && a == 2 && a == 3) {
       console.log('OK');
   }
   
   var a = {
     value: 0,
     valueOf() {
       return ++this.value
     }
   }
   if (a == 1 && a == 2 && a == 3) {
       console.log('OK');
   }
   
   Object.defineProperty()
   
   var a = [1,2,3]
   
   a.toString = a.shift
   
   if (a == 1 && a == 2 && a == 3) {
       console.log('OK');
   }
   ```

7. ```js
   //=>浅克隆：只复制对象或者数组的第一级内容
   //=>深克隆：克隆后数组的每一级都和原始数组没有关联
   //那么请说出，浅克隆都怎么去实现，如何实现深度克隆
   let obj = {
       a: 100,
       b: [10, 20, 30],
       c: {
           x: 10
       },
       d: /^\d+$/
   };
   
   let arr = [10, [100, 200], {
       x: 10,
       y: 20
   }];
   
   function deepClone(obj) {
     if (obj == undefined) return obj
     if (typeof obj !== 'object') return obj
     
     // 如果是正则或者日期函数，我们则创建一个值类似但是不同实例的结果出来
     if (/^(RegExp|Date)$/i.test(constructor.name)) return new constructor(obj);
     
     const Ctor = obj.constructor
     
     let clone = new Ctor;
     
     for (key in obj) {
       if (obj.hasOwnProperty(key)) {
         clone[key] = deepClone(obj[key])
       }
     }
     
     return clone
   }
   
   console.log(deepClone(obj))
   ```

8. ```js
   //=>example：要检测的实例
   //=>classFunc:要检测的类
   function instance_of(example, classFunc) {
       //...
   }
   let res = instance_of([12,23],Array);
   console.log(res); //=>true
   ```

9. 