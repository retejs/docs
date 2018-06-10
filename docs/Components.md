Components
=

Components are designed to increase the ease of development by combining closely related functions of nodes building and processing.

The Component contains [builder](Components.html#node-builders) and [worker](Components.html#node-workers). 

```js
class MyComponent extends Rete.Component {

    constructor(){
        super("My Component"); // name
    }

    builder(node) {
        /// modify node
    }

    worker(node, inputs, outputs) {
        /// process data
    }
}
```

The builder and the worker are executed independently of each other (the first one works once when creating the node, the second one at each [processing](Engine.html#processing)), but in fact they are closely related with each other. Therefore, it makes more sense to consider separately the nodes and together the creation, presentation and processing of the node.

Registering component:
```js
var comp = new MyComponent();

editor.register(comp);
engine.register(comp);
```

## Node builders

These methods have to modify an [Node instance](Nodes.html) and are necessary for the editor [to restore](Editor.html#exportimport-data) all nodes from the JSON data, since the JSON data should store only static information and not the logic of the nodes. Each of the builders must be in the corresponding component:

```js
class NumberComponent extends Rete.Component {
    
    constructor(){
        super('Number');
    }

    builder(node){
        // modify node
        node.addInput(new Rete.Input('Number',numSocket));
        node.addOutput(new Rete.Output('Number',numSocket));
    }
}
```

## Node workers

Workers is a functions for processing  node's data. They receive the parameters `node`, `inputs`, `outputs`. Node data (not node instance), inputs and outputs are corresponds to definitions in the [builders](Components.html#node-builders)


```js
class NumberComponent extends Rete.Component {
    
    constructor(){
        super('Number');
    }

    async worker(node, inputs, outputs){
        // put the node's data or inputs data to outputs
        outputs[0] = node.data.num;
    }
}
```

As you noticed, you can use asynchronous functions (or promises for previous versions of ES). This is necessary in order to perform complex calculations without blocking the main thread (for example, in WebWorker).
