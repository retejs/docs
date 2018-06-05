## Installing

You can take latest build in [Releases](https://github.com/Ni55aN/D3-Node-Editor/releases). Add it and dependencies to your application.

```html
<script src="https://cdn.jsdelivr.net/npm/rete@1.0.0-alpha.2/build/rete.js"></script>
```
Using the build system, you can install a library from npm
```bash
npm install rete rete-alight-render-plugin rete-connection-plugin
```
Import as follows
```js
import Rete from "rete";
import * as ConnectionPlugin from 'rete-alight-render-plugin';
import * as AlightRenderPlugin from 'rete-connection-plugin';
```
Node.js (optionally, only for processing on the server)
```js
import { Engine, ComponentWorker } from "rete/build/rete.engine";
```

## Getting started

![Editor components](https://i.imgur.com/QwPTKUI.png)

Create needed [Sockets](https://github.com/Ni55aN/D3-Node-Editor/wiki/Sockets)
```js
var numSocket = new Rete.Socket('number', 'Number value', 'hint');
```
Define them styles
```css
.socket.number{
    background: #96b38a
}
```

Create some [components](https://github.com/Ni55aN/D3-Node-Editor/wiki/Components)
```js
var numComp = new Rete.Component('Number',{
    builder(node) {
        var out = new Rete.Output('Number',numSocket); 
        var numControl = new Rete.Control('<input type="number">',(element, control)=>{
            control.putData('num', 1);
         });

        return node
                  .addControl(numControl)
                  .addOutput(out);
    },
    worker(node, inputs, outputs){
        outputs[0] = node.data.num;
  }
});

var addComp = new Rete.Component('Add',{
    template: '<div .... ',
    builder(node){
      //...
      return node;
    },
    async worker(node, inputs, outputs){
       await asyncTask();
   }
});

var components = [numComp,addComp];
```
Initialize [context menu](https://github.com/Ni55aN/D3-Node-Editor/wiki/Context-menu) and [node editor](https://github.com/Ni55aN/D3-Node-Editor/wiki/Editor)
```html
<div id="Rete" class="node-editor"></div>
```
```js

var menu = new Rete.ContextMenu({
                'Actions':{
                    'Action': function(){alert('Subitem selected');}
                },
                'Nodes':{
                    'Number': numComp, 
                    'Add': addComp
                }
           });
var container = document.querySelector('#Rete');
var editor = new Rete.NodeEditor('demo@0.1.0', container, components, menu);
```
Use the [Engine](https://github.com/Ni55aN/D3-Node-Editor/wiki/Engine) to start processing the data (also [avaliable](https://github.com/Ni55aN/D3-Node-Engine) cross-platform Engine)
```js
var engine = new Rete.Engine('demo@0.1.0');

editor.eventListener.on('change', async () => {
    await engine.abort();            
    var status = await engine.process(editor.toJSON());            
});
```
Full code and example you can take on the [Codepen](https://codepen.io/Ni55aN/pen/jBEKBQ)

## Architecture

![](https://i.imgur.com/MKPpJU9.png)

&nbsp;

![](https://i.imgur.com/P8E61o6.png)