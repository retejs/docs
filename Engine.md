Engine
-

This component allows you to process [Flow-based](https://en.wikipedia.org/wiki/Flow-based_programming) data in nodes and transfer them from an output to input. The engine is independent of other components of the editor. All it needs is an [identifier](Editor#identifier), [workers](Engine/#node-workers) from components and [JSON data](Editor#exportimport-data).

```js
var engine = new Rete.Engine('demo@0.1.0');

engine.register(myComponent);
await engine.process(data); 
```

It can work independently and without knowing anything about NodeEditor.

## Processing

Consider the situation when during the work in the editor you need to immediately receive the results of the changes (this is easy to do with [`change` event](Events))

```js
editor.on('process', () => {
    engine.process(editor.toJSON()); // imagine that it could take one second of time
});
```
The user will be annoyed that the editor will hang. In fact, the process method is also asynchronous, so you can call it many times without waiting for the previous one to complete. To avoid this, use the abort method, which waits until the previous processing is canceled.

```js
editor.on('process', async () => {
    await engine.abort();
    await engine.process(editor.toJSON());   
});
```
Now this gives a guarantee that at one time only one processing will be performed, and the previous one will be canceled when the data is changed


Usually there is some main node from which the processing should start or all data go to it, then you can specify it:

```js
engine.process(data, node.id); 
```

If in any situation you need the data that you specify when processing

```js
engine.process(data, null, arg1, arg2); 
```

To each worker executed by this process will transfared this arguments

```js
worker(node, inputs, outputs, arg1, arg2){
     outputs[0] = node.data.num;
}
```

If an error occurs during processing (detected recursion, wrong startId, invalid data), you can get its message and data (if any)
```js
engine.on('error', ({ message, data }) => {

});
```

## Cross-platform
[D3-Node-Engine for C++](https://github.com/retejs/cpp-engine)

