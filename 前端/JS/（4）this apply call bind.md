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

### call

`A.call(B)`

`that.fn = this`: 将A方法作为B的属性，然后调用这个属性，this就指向了A。

```js
Function.prototype.myCall = function(that) {
  if(typeof this !== 'function') {
    throw new TypeError('当前this不是一个function')
  }

  that = that || window
  that.fn = this // 绑定this
  const result = that.fn([...arguments].slice(1))

  delete that.fn  // 删除这个属性
  return result
}
```

### apply

```js
Function.prototype.myApply = function(that) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  that = that || window
  that.fn = this
  let result
  if (arguments[1]) {
    result = that.fn(...arguments[1])
  } else {
    result = that.fn()
  }
  delete that.fn
  return result
}
```

### bind

```js
Function.prototype.myBind = function (that) {
  if (typeof this !== 'function') {
    throw new TypeError('Error')
  }
  const _this = this
  const args = [...arguments].slice(1)
  // 返回一个函数
  return function F() {
    // 如果new 生成实例，this不会被改变， 可以忽略传入的that，就相当于new _this
    if (this instanceof F) {
      return new _this(...args, ...arguments)
    }
    return _this.apply(that, args.concat(...arguments))
  }
}
```
