## iframe通信

### 相关Api语法
`otherWindow.postMessage(message, targetOrigin, [transfer]);`

**语法**

1. `otherWindow` : 其他窗口的一个引用，比如iframe的contentWindow属性
2. `message`: 将要发送到其他 window的数据
3. `targetOrigin`: 通过窗口的origin属性来指定哪些窗口能接收到消息事件，其值可以是字符串"*"（表示无限制）或者一个URI, 目标的协议、主机地址或端口必须匹配，否则不发送
4. `transfer`: 是一串和message 同时传递的 Transferable 对象. 这些对象的所有权将被转移给消息的接收方，而发送一方将不再保有所有权(注意上面是放在[ ]里面)

---

`window.addEventListener("message", function(event){}, false);`

**语法**

event上的数据

1. `data`: 从其他 window 中传递过来的对象
2. `origin`: 调用 postMessage  时消息发送方窗口的 origin,( 这个字符串由 协议、“://“、域名、“ : 端口号”拼接而成。例如 “https://example.org (隐含端口 443)”)
3. `source`: 对发送消息的窗口对象的引用; 您可以使用此来在具有不同origin的两个窗口之间建立双向通信


### 例子

**例子1：**

普通通信-主页面

```js
 window.onload = function() {
    const iframe = document.getElementById('iframe');
    otherWindow = iframe.contentWindow;
    otherWindow.postMessage('来自主页面的消息', '*')
    window.addEventListener('message', function(e){
      console.log('主页面收到的消息', e);
    }, false)
  }
```

普通通信-iframe页面

```js
window.addEventListener('message', function(event){
    console.log('iframe', event)
    top.postMessage('你好,主页面', '*')
  }, false)
```

**例子2:**

MessageChannel通信-主页面

```js
 window.onload = function() {
    const { port1, port2 } = new MessageChannel;

    const iframe = document.getElementById('iframe');
    const otherWindow = iframe.contentWindow;
    // 讲管道port2传输过去
    otherWindow.postMessage('你好iframe', '*', [port2])

    // 定义port1管道的方法
    port1.onmessage = function(e) {
      console.log('来自port2的消息', e)
      port1.postMessage('谢谢你iframe')
    }
  }
```

MessageChannel通信-iframe页面

```js
window.addEventListener('message', function(e){
    console.log('iframe', e)
    // top.postMessage('你好,主页面', '*')
    // 发送消息
    e.ports[0].postMessage('你好主页面')
    // 定义port2的方法
    e.ports[0].onmessage = function(e) {
      console.log('来自port1的消息', e)
    }
  }, false)
```