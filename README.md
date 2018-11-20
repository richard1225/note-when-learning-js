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
