## node自定义事件

events 模块只提供了一个对象： events.EventEmitter。

EventEmitter 的核心就是事件触发与事件监听器功能的封装。

```js
//引入events
var EventEmitter = require('events').EventEmitter; 
//实例化
var event = new EventEmitter(); 
//添加自定义事件监听
event.on('some_event', function() { 
    console.log('some_event 事件触发'); 
}); 

//1s后调用自定义事件
setTimeout(function() { 
    event.emit('some_event'); 
}, 1000);

//some_event 事件触发
```

**对于同一事件的多个监听回调**

当事件触发时，注册到这个事件的**事件监听器被依次调用**，事件参数作为回调函数参数传递。

```js
var events = require('events'); 
var emitter = new events.EventEmitter(); 
emitter.on('someEvent', function(arg1, arg2) { 
    console.log('listener1', arg1, arg2); 
}); 
emitter.on('someEvent', function(arg1, arg2) { 
    console.log('listener2', arg1, arg2); 
}); 
emitter.emit('someEvent', 'arg1 参数', 'arg2 参数'); 

//listener1 arg1 参数 arg2 参数
//listener2 arg1 参数 arg2 参数

```

**newListener**

- **event** - 字符串，事件名称
- **listener** - 处理事件函数

该事件在添加新监听器时被触发。

```js
...
//这里使用on来添加监听也行
myEvent.addListener('newListener', () => {
    console.log('新增一个connection事件监听');
})
myEvent.on('connection', function (args) {
    console.log('connection事件触发' + args);
})
...

//新增一个connection事件监听
```

## node命令行工具

https://juejin.cn/post/6857842033084760071