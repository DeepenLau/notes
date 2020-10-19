1. ```js
   console.log(a, b, c); // undefined, undefined, undefined
   var a = 12,
       b = 13,
       c = 14;
   function fn(a) {
       console.log(a, b, c); // 10, 13, 200
       a = 100;
       c = 200;
       console.log(a, b, c); // 100, 13, 200
   }
   b = fn(10);
   console.log(a, b, c); // 12, undefined, 200
   ```

2. ```js
   var i = 0;
   function A() {
       var i = 10;
       function x() {
           console.log(i);
       }
       return x;
   }
   var y = A();
   y(); // 10
   function B() {
       var i = 20;
       y(); // 10
   }
   B();
   ```

3. ```js
   var a=1;
   var obj ={
      name:"tom"
   }
   function fn(){
      var a2 = a;
      obj2 = obj;
      a2 =a;
      obj2.name ="jack";
   }
   fn();
   console.log(a); // 1
   console.log(obj); // { name: 'jack' }
   ```

4. ```js
   var a = 1;
   function fn(a){
       console.log(a) // function a(){}
       var a = 2;
       function a(){}
   }
   fn(a);
   ```

5. ```js
   console.log(a); // undefined
   var a=12; 
   function fn(){
       console.log(a); // undefined
       var a=13;   
   }
   fn();   
   console.log(a); // 12
   
   ----
   
   console.log(a); // undefined
   var a=12;
   function fn(){
       console.log(a); // 12
       a=13;
   }
   fn();
   console.log(a); // 13
   
   ----
   
   console.log(a); // 报错 => a is not defined
   a=12;
   function fn(){
       console.log(a);
       a=13;   
   }
   fn();
   console.log(a);
   ```

6. ```js
   var foo='hello'; 
   (function(foo){
      console.log(foo); // hello
      var foo=foo||'world';
      console.log(foo); // hello
   })(foo);
   console.log(foo); // hello
   ```

7. ```js
   // *
   {
     	// 这里函数变量提升，在块级上下文是 声明加赋值，同时映射给上级作用域一份，但是只声明，不赋值
       function foo() {}
     	// 在这一行之前，对 foo 的赋值操作都会给上级上下文中的同名映射变量一样的操作
       foo = 1;
   }
   console.log(foo); // foo
   
   // ----
   
   {
       function foo() {}
       foo = 1;
     	// 在这一行之前，对 foo 的赋值操作都会给上级上下文中的同名映射变量一样的操作
       function foo() {}
   }
   console.log(foo); // 1
   
   // ----
   
   {
       function foo(a) {}
       foo = 1;
       function foo(b) {} // 变量提升 foo = 1 更加晚执行
       foo = 2;
   }
   console.log(foo); // 1
   
   ```

8. ```js
   /** 
    * EC(G)
    *   创建函数执行上下文
    *     函数执行上下文
    *       变量提升
    *         AO：x，y
    *       形参赋值：发现有默认参数，函数体里没有 var/let/const/function, 不会创建一个块级上下文
    *       完成函数形参赋值，覆盖块级上下文中的同名变量
    *         AO： x = 5，y = function anonymous1() {x = 2})
    *         
    *     执行代码
    *       x = 3 // AO: x = 3
    *       y() => AO：x = 2
    *.      console.log(x) => 输出 AO 中的 x
    */
   var x = 1
   function func (x, y = function anonymous1() {x = 2}) {
     x = 3
     y()
     console.log(x) // 2
   }
   func(5)
   console.log(x) // 1
   ```

   ```js
   /** 
    * EC(G)
    *  VO(G): x
    */
   var x = 1
   /** 
    * EC(G)
    *  VO(G): x
    *  创建函数执行上下文
    *    形参赋值：AO: x, y
    *      发现有形参默认值，函数体里有 var/let/const/function 声明的变量，创建块级上下文
    *      函数体里面的范围属于块级上下文
    *        块级上下文变量提升
    *          VO(B):  x
    *      完成函数形参赋值，给AO中的变量赋值的同时覆盖VO(B)中的同名变量
    *         AO: x, y = function anonymous1() {x = 2}
    *         VO(B): x, VO(B)里面没有声明y
    *   执行代码：
    *     x = 3 => VO(B): x = 3
    *     y() => AO: x = 2
    *.    console.log(x) => VO(B)中的 x => 3
    */
   function func(x, y = function anonymous1() {x = 2}) {
     var x = 3
     y()
     console.log(x) // 3
   }
   func()
   console.log(x) // 1
   ```

   

9. ```js
   var x = 1
   /** 
    * 有形参默认值，函数体里有 var/let/const/function 声明的变量，创建块级上下文
    * AO:    x, y = function anonymous1() {x=2} => x = 2
    * VO(B): x, y = function anonymous1() {x=2} => x = 3, y = function anonymous1() {x = 4}
    */
   function func(x, y = function anonymous1() {x=2}) {
     var x = 3
     var y = function anonymous1() {x = 4}
     y()
     console.log(x) // 4 (VO(B))
   }
   func(5)
   console.log(x) // 1
   ```

数据类型

1. ```js
   let result = 100 + true + 21.2 + null + undefined + "Tencent" + [] + null + 9 + false;
   console.log(result);
   
   // 100 + 1 + 21.2 + 0 + NaN + 'Tencent' + '' + 'null' + '9' + 'false'
   // 'NaNTencentnull9false'
   ```

2. ```js
   {}+0?alert('ok'):alert('no'); // +0 => 'no'
   0+{}?alert('ok'):alert('no'); // '0[object Object]' => 'ok'
   ```

3. ```js
   let res = Number('12px'); // NaN
   if(res===12){
       alert(200);
   }else if(res===NaN){
       alert(NaN);
   }else if(typeof res==='number'){
       alert('number');
   }else{
       alert('Invalid Number');
   }
   // 'number'
   ```

4. ```js
   let arr = [27.2,0,'0013','14px',123];
   arr = arr.map(parseInt);
   console.log(arr);
   
   // 第二个参数范围 2-36
   parseInt(27.2, 0)   // 27
   parseInt(0, 1)      // NaN
   parseInt('0013', 2) // 001 =>  0*2^2 + 0*2^1 + 1*2^0 = 0 + 0 + 1 = 1
   parseInt('14px', 3) // 1 => 1*3^0 = 1
   parseInt(123, 4)    // 1*4^2 + 2*4^1 + 3*4^0 = 16 + 8 + 3 = 27
   ```

闭包作用域

1. ```js
   var a = 10,
       b = 11,
       c = 12;
   function test(a) {
       a = 1;
       var b = 2;
       c = 3;
   }
   test(10);
   console.log(a, b, c); // 10, 11, 3
   
   
   ```

2. ```js
   var a = 4;
   function b(x, y, a) { // 1, 2, 3
       console.log(a); // 3
       arguments[2] = 10; 
       console.log(a); // 10
   }
   a = b(1, 2, 3); // 
   console.log(a); // undefined
   
   
   ```

3. ```js
   var a = 9;
   function fn() {
       a = 0;
       return function (b) {
           return b + a++;
       }
   }
   var f = fn();
   console.log(f(5));    // 5 / VO(G):a = 1
   console.log(fn()(5)); // 5
   console.log(f(5));    // 6 / VO(G):a = 2
   console.log(a);       // 2
   ```

4. ```js
   var test = (function (i) { // 2
       return function () {
           alert(i *= 2);
       }
   })(2);
   test(5); // '4'
   ```

5. ```js
   var x = 4;
   function func() {
       return function(y) { // 6
           console.log(y + (--x)); // 6 + 3
       }
   }
   var f = func(5);
   f(6);         // VO(G): x = 3,   9 
   func(7)(8);   // VO(G): x = 2 , 10 
   f(9);         // VO(G): x = 1,  10
   console.log(x); // 1
   ```

6. ```js
   var x = 5,
       y = 6;
   function func() {
       x += y;
       func = function (y) {
           console.log(y + (--x));
       };
       console.log(x, y);
   }
   func(4);            // x = 11 /   x = 11, y = 6
   func(3);            // x = 10 /   13
   console.log(x, y);  // 10, 6
   ```

7. ```js
   function fun(n, o) {
       console.log(o);
       return {
           fun: function (m) {
               return fun(m, n);
           }
       };
   }
   var c = fun(0).fun(1); // n = 0, o = undefined / m = 1, n = 0 / n = 1, o = 0
   c.fun(2); // m = 2, n = 1
   c.fun(3); // m = 3, n = 1
   
   // undefined, 0
   // 1
   // 1
   ```

8. ```js
   // 下面代码输出的结果是多少，为什么？如何改造一下，就能让其输出 20 10？
   var b = 10;
   (function b() {
       b = 20;
       console.log(b); // b 函数
   })();
   console.log(b); // 10
   
   // 改造
   var b = 10;
   (function b() {
      var b = 20;
       console.log(b); // 20
   })();
   console.log(b); // 10
   ```

9. ```js
   let res = fn(1,2)(3);
   console.log(res); //=>6  1+2+3
   
   function fn(x, y) {
     return function (z) {
       return x + y + z
     }
   }
   ```

10. ```js
    /* 
        在函数式编程当中有一个很重要的概念就是函数组合， 实际上就是把处理数据的函数像管道一样连接起来， 然后让数据穿过管道得到最终的结果。 例如：
        const add1 = (x) => x + 1;
        const mul3 = (x) => x * 3;
        const div2 = (x) => x / 2;
        div2(mul3(add1(add1(0)))); //=>3
    ​
        而这样的写法可读性明显太差了，我们可以构建一个compose函数，它接受任意多个函数作为参数（这些函数都只接受一个参数），然后compose返回的也是一个函数，达到以下的效果：
        const operate = compose(div2, mul3, add1, add1)
        operate(0) //=>相当于div2(mul3(add1(add1(0)))) 
        operate(2) //=>相当于div2(mul3(add1(add1(2))))
    ​
        简而言之：compose可以把类似于f(g(h(x)))这种写法简化成compose(f, g, h)(x)，请你完成 compose函数的编写 
    */
    const add1 = (x) => x + 1;
    const mul3 = (x) => x * 3;
    const div2 = (x) => x / 2;
    
    function compose(...fns) {
      return function (x) {
        return fns.reduceRight((prevResult, fn) => {
          return fn(prevResult)
        }, x)
      }
    }
    
    const operate = compose(div2, mul3, add1, add1)
    
    operate(0)
    operate(2)
    ```

this

1. ```js
   var num = 10; // 65
   var obj = {
       num: 20
   };
   obj.fn = (function (num) { // 20
       this.num = num * 3; // 60
       num++; // 21
       return function (n) { // 5     / 10
           this.num += n; // 60 + 5   / obj.num = 20 + 10 = 30
           num++; // 22               / 23
           console.log(num); // 22    / 23
       }
   })(obj.num);
   var fn = obj.fn;
   fn(5); // 22
   obj.fn(10); // 23
   console.log(num, obj.num); // 65, 30
   ```

2. ```js
   let obj = {
       fn: (function () {
           return function () {
               console.log(this);
           }
       })()
   };
   obj.fn(); // obj
   let fn = obj.fn;
   fn(); // window
   ```

3. ```js
   var fullName = 'language';
   var obj = {
       fullName: 'javascript',
       prop: {
           getFullName: function () {
               return this.fullName;
           }
       }
   };
   console.log(obj.prop.getFullName()); // undefined
   var test = obj.prop.getFullName;     
   console.log(test());                 // language
   ```

4. ```js
   var name = 'window';
   var Tom = {
       name: "Tom",
       show: function () {
           console.log(this.name);
       },
       wait: function () {
           var fun = this.show;
           fun();
       }
   };
   Tom.wait(); // window
   ```

5. ```js
   window.val = 1; // 2 / 4
   var json = {
       val: 10, // 20
       dbl: function () {
           this.val *= 2;
       }
   }
   json.dbl();
   var dbl = json.dbl;
   dbl();
   json.dbl.call(window);
   alert(window.val + json.val);// 4 + 20 => '24'
   ```

6. ```js
   (function () {
       var val = 1; // 2
       var json = {
           val: 10,
           dbl: function () {
               val *= 2;
           }
       };
       json.dbl();
       alert(json.val + val); // 10 + 2 => '12'
   })();
   ```

7. 



