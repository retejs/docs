Plugins
=

### Conection [![npm](https://img.shields.io/npm/v/rete-connection-plugin.svg)](https://www.npmjs.com/package/rete-connection-plugin)

```js
import * as ConnectionPlugin from 'rete-connection-plugin';

editor.use(ConnectionPlugin, { curvature: 0.4 });
```
This plugin is always required for full-fledged work of the editor, as it is responsible for displaying and managing connections.


### Alight Renderer [![npm](https://img.shields.io/npm/v/rete-alight-render-plugin.svg)](https://www.npmjs.com/package/rete-alight-render-plugin)

```js
import * as AlightRenderPlugin from 'rete-alight-render-plugin';

editor.use(AlightRenderPlugin, { template: '<div ...>' }); // global template

class MyComponent extends Component {
    constructor(){
        // ...
        this.template = '<div ...>'; // component specific template
    }
}
```
It's also always necessary plugin, but it can be replaced with the same plugin that uses a library other than Angular Light to render the data


### Context menu [![npm](https://img.shields.io/npm/v/rete-context-menu-plugin.svg)](https://www.npmjs.com/package/rete-context-menu-plugin)

```js
import * as ContextMenuPlugin from 'rete-context-menu-plugin';

editor.use(ContextMenuPlugin);
```
Current version of this plugin does not have such rich functionality as a menu in v0.7.4, and only displays all registered nodes in the form of one list.

### Keyboard [![npm](https://img.shields.io/npm/v/rete-keyboard-plugin.svg)](https://www.npmjs.com/package/rete-keyboard-plugin)

```js
import * as KeyboardPlugin from 'rete-keyboard-plugin';

editor.use(KeyboardPlugin);
```
Handles keydown events for keys such as "Delete" (remove node) or "Space" (open context menu)

### Module [![npm](https://img.shields.io/npm/v/rete-module-plugin.svg)](https://www.npmjs.com/package/rete-module-plugin)

```js
import * as ModulePlugin from 'rete-module-plugin';

editor.use(ModulePlugin, { engine, modules });
```

where `modules` is an assocuative array with objects

For instance:

```js
var modules = {
  'index.rete': { data: initialData() }
}
```

There should be at least 3 types of nodes: Input, Output and Module. Their components should have the following properties respectively:
```js
this.module = {
    nodeType: 'input',
    socket: numSocket
}

this.module = {
    nodeType: 'output',
    socket: numSocket
}

this.module = {
    nodeType: 'module'
}
```

The plugin itself will add inputs and outputs to the Module node, but in the rest you must add them manually


[Example](https://github.com/retejs/examples/tree/master/Module)

### Profiler [![npm](https://img.shields.io/npm/v/rete-profiler-plugin.svg)](https://www.npmjs.com/package/rete-profiler-plugin)

```js
import * as ProfilerPlugin from 'rete-profiler-plugin';

engine.use(ProfilerPlugin, { editor, enabled: true }); // editor can be optional
```

This plugin adds an element to each node to display the elapsed time by the worker, and prints the builder time to the console.

### Readonly [![npm](https://img.shields.io/npm/v/rete-readonly-plugin.svg)](https://www.npmjs.com/package/rete-readonly-plugin)

```js
import * as ReadonlyPlugin from 'rete-readonly-plugin';

editor.use(ReadonlyPlugin, { enabled: false });
```

This plugin prevents a follow events:

- keydown
- nodetranslate
- nodeselect
- connectioncreate
- connectionremove
- nodecreate
- noderemov

Thus, thanks to the event architecture, these functions can be implemented without interfering with the library code.

### Task [![npm](https://img.shields.io/npm/v/rete-task-plugin.svg)](https://www.npmjs.com/package/rete-task-plugin)

```js
import * as TaskPlugin from 'rete-task-plugin';

editor.use(TaskPlugin);
```

Example of use:

```js
// inside of compoent's constructor
this.task = {
    outputs: {num1: 'option', num2: 'output'},
    init(task) {  // сalled when initializing all tasks (at the engine.process())
        task.run('any data');
        task.reset();
    }
}

// workers should looks like follows:
worker(node, inputs, data) { // data is 'any data' from run()
    console.log('Keydown event', node.id, data);
    // inputs['inp_num1']
    this.closed = ['num1']; // prevents the call of the Tasks, which are connected to the current task through the first 'option' socket
    return {num2: data} // return output data
}
```

[Full code](https://github.com/retejs/examples/tree/master/Tasks)