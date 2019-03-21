### this

**this指向`最后`一个调用它的对象**

```js
var name = "window";
var a = {
    name: "kangkang",
    fn : function () {
        console.log(this.name);      // kangkang
    }
}
window.a.fn();

// 如果a 没有name  则打印undefined
```

```js
var name = "window";
var a = {
    name : null,
    fn : function () {
        console.log(this.name);      // window
    }
}

var f = a.fn;
f();

// f赋值到了window上
```