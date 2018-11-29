# 箭头函数 和 this 的理解

1. 箭头函数不会创建自己的 this,它只会从自己的作用域链的上一层继承 this

```
outside = 1 // window上下文

var outfunc = function(){
  this.outside = 123 // 如果不使用new outfunc(), 则是window 上下文， 如果使用new outfunc，则是构造函数新创建的this.
  return ()=>{ console.log(this.outside) } // 继承函数内的上下文this
}

var infunc = new outfunc() // 使用new新建对象， outfunc中的this为新建的this，属于outfunc，
infunc() // 输出为outfunc中的outside， 值为123
outside // 输出为123，已经被outfunc修改


// ---------------------------------------------------------------
var infunc = outfunc() // 如果不使用new， outfunc上下文的this是调用他的对象的this，这里是从window调用了outfunc，所以this是window的上下文
infunc() // 输出为windows中的outside， 值为123
outside // 输出为123，已经被outfunc修改
```

# new 用法 和 函数中this的关系
给出如下函数，并分别不使用new调用，和使用new调用 `diffNew`函数
> 代码中 > 符号表示console.log的输出, 双斜杠//右侧 表示返回值
```
function diffNew(){
  notInThis = 'Not in this'
  this.inThis = 'in this'
  console.log(this.inThis)
}
```
### 1. Not use new
```
notNewCall = diffNew() // undefined
> in this

console.log(this.inThis)
> in this

delete thid.inThis 
```
在调用函数时，函数返回了undefined，这是因为`diffNew`函数没有给出返回值，然后输出了in this，调用函数所输出的`in this`并不是来自函数内部的this，而是来自全局变量window的this。
> 为什么此时函数中的this是来自于window呢？因为在直接调用函数的方式中，this是来自调用这个函数的对象所提供的。你会问：这时候没有对象调用diffNew啊！原来，此时diffNew是被定义在全局变量window中，并在window的上下文中调用，所以是相当于window.diffNew()的调用方式，diffNew中此时的this当然就是window提供的啦

`diffNew`函数中的this赋值语句，给全局window的this新加了一个inThis属性。

从第二行log可知，window被新加了一个inThis属性，为了不影响接下来的测试，我们把window中的inThis给删掉
### 2. Use new
```
notNewCall = new diffNew() // {inThis: "in this"}
> in this

console.log(this.inThis)
> undefinded
```
* 通过new的方式来调用函数，实际上diffNew()就是一个构造函数，来创建一个对象，`这时函数中的this就与调用函数的对象无关，不来自于window，是新建一个函数自己的this`。
* 如果没有写return，new diffNew()执行完之后，会返回{...this}这个对象。
* 当前的this只有inThis，而没有notInThis变量，因为this是对象属性，和词法作用域中的变量链没有任何关系，所以notInThis不会被加入this中，除非手动this添加

输出window中的inThis，window没有被添加inThis属性。证明函数中的this不来自window，而来自函数新建的this本身

