Migrate from v0.7.4 to v1.0.0
=


Sockets
-

### v0.7.4
```js
var numSocket = new D3NE.Socket("number", "Number value", "hint");
```
### v1.0.0
```js
var numSocket = new Rete.Socket('Number value');
```

Components
-

### v0.7.4
```js
var componentNum = new D3NE.Component("Number", {
   builder(node) {
       // ...
   },
   worker(node, inputs, outputs) {
       // ...
   }
});
```
### v1.0.0
```js
class NumComponent extends Rete.Component {

    constructor(){
        super("Number");
    }

    builder(node) {
        // ...
    }

    worker(node, inputs, outputs) {
        // ...
    }
}
```

Editor
-

### v0.7.4
```js
var components = [componentNum];
var editor = new D3NE.NodeEditor("demo@0.1.0", container, components, menu);   
editor.use(AlightRenderPlugin);
editor.use(ConnectionPlugin);
```
### v1.0.0
```js
var components = [new NumComponent()];
var editor = new Rete.NodeEditor('demo@0.1.0', container);

components.map(c => editor.register(c));
```

Engine
-

### v0.7.4
```js
var engine = new D3NE.Engine("demo@0.1.0", components);
```
### v1.0.0
```js
var engine = new Rete.Engine('demo@0.1.0');
components.map(c => engine.register(c));
```

Nodes
-

### v0.7.4
```js
var node = componentNum.newNode();
node.data.num = 2;
await componentNum.builder(node);
```
### v1.0.0
```js
await componentNum.createNode({num: 2});
```

Events
-

### v0.7.4
```js
editor.eventListener.on("change", async function(_, persist) {
    /// persist 
});
editor.eventListener.trigger("change")
```
### v1.0.0
```js
editor.on("process", async function() {
    /// editor.silent
});
engine.on('error', () => {} )
editor.trigger("process")
```

ContextMenu
-

### v0.7.4
```js
var menu = new D3NE.ContextMenu({
   Values: {
      Value: componentNum,
      Action: function() {
         alert("ok");
      }
   },
   Add: componentAdd
});
```
### v1.0.0
```js
editor.use(ContextMenuPlugin);
```

Controls
-

### v0.7.4
```js
var numControl = new D3NE.Control('<input type="number">',
    (el, c) => {
    el.value = c.getData('num') || 1;
    
    function upd() {
        c.putData("num", parseFloat(el.value));
    }

    el.addEventListener("input", ()=>{
        upd();
        editor.eventListener.trigger("change");
    });
    el.addEventListener("mousedown", function(e){e.stopPropagation()});// prevent node movement when selecting text in the input field
    upd();
    }
);
```

### v1.0.0 (with alight-render-plugin)
```js

class NumControl extends Rete.Control {

    constructor(emitter, key, readonly) {
        super();
        this.emitter = emitter;
        this.key = key;
        this.template = '<input type="number" :readonly="readonly" :value="value" @input="change($event)"/>';

       this.scope = {
            value: 0,
            readonly,
            change: this.change.bind(this)
        };
    }

    change(e){
        this.scope.value = +e.target.value;
        this.update();
    }

    update(){
        if(this.key)
            this.putData(this.key, this.scope.value)
        this.emitter.trigger('process');
        this._alight.scan();
    }

    mounted() {
        this.scope.value = this.getData(this.key) || 0;
        this.update();
    }

    setValue(val) {
        this.scope.value = val;
        this._alight.scan()
    }
}
```

Builders/Workers
-

### v0.7.4
```js
builder(node){
    editor // you need to import the editor yourself
}
```
### v1.0.0
```js
builder(node){
    this.editor // editor is available in the builder and worker
}
```

Tasks
-

### v0.7.4
```js
worker(node, inputs, outputs) {
    var task = new D3NE.Task(inputs, function (inps, data) {
        this.closed = [1];
    });
    startTask =  task;

    outputs[0] = task.option(0);
    outputs[1] = task.output(1);
}
```
### v1.0.0
```js
constructor(){
    // super
    this.task = {
        outputs: ['option', 'output'],
        init(task){
            startTask =  task;
        }
    }
}


worker(node, inputs, data) {
    this.closed = [1];
}
```

Modules
-

### v0.7.4
```js
var moduleManager = new D3NE.ModuleManager(['Input'], ['Output']);

// --
    moduleManager.getInputs(node.data.module.data).forEach(i => {
        if (i.title == 'Input')
            node.addInput(new D3NE.Input(i.name, numSocket));
        /// else for another socket
    });

    moduleManager.getOutputs(node.data.module.data).forEach(o => {
        node.addOutput(new D3NE.Output(o.name, numSocket));
    });
// ...

    worker: moduleManager.workerModule.bind(moduleManager)
});

// ---
    worker: moduleManager.workerInputs.bind(moduleManager)
// ---
    worker: moduleManager.workerOutputs.bind(moduleManager)
```
### v1.0.0
```js
    this.module = {
        nodeType: 'module'
    }
// ---
    this.module = {
        nodeType: 'input',
        socket: numSocket
    }
// ---
    this.module = {
        nodeType: 'output',
        socket: numSocket
    }
```
