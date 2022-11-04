# console的花式打印
## **占位符**

<font style="background: #f8f8f8; color: #e96900;">%o</font>：用对象代替
```
const user = { name: 'ming', age: 10 }
console.log('hey %o, here is your details in form of object', user);
```
![console占位符%o](../images/console%E5%8D%A0%E4%BD%8D%E7%AC%A6o.png)


<font style="background: #f8f8f8; color: #e96900;">%s</font>：用字符串代替
```
const user = { name: 'ming', age: 10 }
console.log('hey %s, here is your details in form of object', 'ming', user);
```
![console占位符%s](../images/console%E5%8D%A0%E4%BD%8D%E7%AC%A6s.png)


<font style="background: #f8f8f8; color: #e96900;">%d</font>：用整数数字代替
```
console.log('%d年%d月',new Date().getFullYear(), new Date().getMonth() + 1);
```
![console占位符%d](../images/console%E5%8D%A0%E4%BD%8D%E7%AC%A6d.png)


<font style="background: #f8f8f8; color: #e96900;">%f</font>：用小数数字代替
```
console.log('%f', Math.PI);
```
![console占位符%f](../images/console%E5%8D%A0%E4%BD%8D%E7%AC%A6f.png)



<font style="background: #f8f8f8; color: #e96900;">%c</font>：为输出添加css
```
console.log('%c Have Css Text ', 'background-color: #606060; color: #fff; border-radius: 3px;');
```
![console占位符%c](../images/console%E5%8D%A0%E4%BD%8D%E7%AC%A6c.png)



## **方法**

### **输出信息类型**
```
console.log('默认消息！');
console.info('提示消息！');
console.error('错误消息！');
console.warn('警告消息！');
```
![console方法输出信息类型](../images/console%E6%96%B9%E6%B3%95%E8%BE%93%E5%87%BA%E7%B1%BB%E5%9E%8B.png)


### **执行时间**
```
console.time();
for (let i = 0; i < 100000; i++) {}
console.timeEnd();
```
![console方法执行时间](../images/console%E6%96%B9%E6%B3%95%E6%89%A7%E8%A1%8C%E6%97%B6%E9%97%B4.png)


### **分组输出**
```
console.group('前端');
console.log('前端-react.js');
console.log('前端-vue.js');
console.log('前端-angular.js');
console.groupEnd();
```
![console方法分组输出](../images/console%E6%96%B9%E6%B3%95%E5%88%86%E7%BB%84.png)


### **调用计数**

这个方法可以打印count() 被调用的次数，方法接受一个参数 label 如果提供label 这个方法就会按照 label 来统计次数
```
let name = "";
function greet() {
  console.count(name);
  return "hi " + name;
}
name = "bob";
greet();
name = "alice";
greet();
greet();
console.count("alice");
```
![console方法调用计数1](../images/console%E6%96%B9%E6%B3%95%E8%B0%83%E7%94%A8%E8%AE%A1%E6%95%B02.png)


如果没有提供参数，这个方法就会统计所处的位置上的调用次数。
```
function countFn() {
  console.count('当前执行的次数');
}
countFn();
countFn();
countFn();
```
![console方法调用计数2](../images/console%E6%96%B9%E6%B3%95%E8%B0%83%E7%94%A8%E8%AE%A1%E6%95%B01.png)


### **表格**
```
const person = {
    name: 'ming',
    age: 10
};
console.table(person)
```
![console方法表格](../images/console%E6%96%B9%E6%B3%95%E8%A1%A8%E6%A0%BC.png)


### **条件断言**

接收两个参数，第一个参数的值或返回值为false的时候，将会在控制台上抛出一个异常并将其余参数作为异常描述输出
```
console.assert(false, '输出异常信息', 123);
console.assert(true, '不输出');
```
![console方法条件断言](../images/console%E6%96%B9%E6%B3%95%E6%9D%A1%E4%BB%B6%E6%96%AD%E8%A8%80.png)


### **堆栈跟踪**

这个方法用来追踪函数的调用轨迹
```
function foo() {
  function bar() {
    console.trace();
  }
  bar();
}
foo();
```
![console方法堆栈跟踪](../images/console%E6%96%B9%E6%B3%95%E5%A0%86%E6%A0%88%E8%B7%9F%E8%B8%AA.png)


#### **清除输出**
清理掉所有打印的信息
```
console.clear()
```
![console清除输出1](../images/console%E6%96%B9%E6%B3%95%E6%B8%85%E9%99%A4%E8%BE%93%E5%87%BA1.png)


如果在浏览器勾选了 Preserve log，那么此方法会被禁止
![console清除输出2](../images/console%E6%96%B9%E6%B3%95%E6%B8%85%E9%99%A4%E8%BE%93%E5%87%BA2.png)

		

### **内存**

console.memory 是 console 对象的一个属性，而不是一个方法。它可以展示 javaScript 应用使用了多少浏览器的内存
```
console.memory
```
![console方法内存](../images/console%E6%96%B9%E6%B3%95%E5%86%85%E5%AD%98.png)

