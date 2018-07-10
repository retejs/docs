Editor
=

![components](assets/editor.png)

`NodeEditor` presents an area with nodes and connections beetween their sockets:
- Available interaction with the working area (moving, zoom) and managing nodes (move, add, delete)
- Each node can have inputs and outputs through which connections to other nodes are created
- Handle editor [events](Events)
- All editor data can be [exported and imported](Editor#exportimport-data) from JSON format

Create the instance of NodeEditor:
```js
var editor = new Rete.NodeEditor('demo@0.1.0', container);
```
The `demo@0.1.0` parameter is the [identifier](Editor#identifier) of your app/editor

The `container` is a simple HTMLelement (div, usually)

## Identifier

Identifier consists of the app name and version. The version is needed to control the import of data into your editor, since the data to previous versions can be incompatible with the current version of your editor (where the main role is played by the Node builders, see below). The same rule exists for the [Engine](Engine), which allows to prevent incompatibility of data with implementations in [node workers](Engine#node-workers)


## Export/import data

To save the added nodes in the editor and all connections to them simply call the `toJSON` method of the object *NodeEditor*
```js
var data = editor.toJSON()
```
Tht data have about the following structure:
```json
{  
   "id":"demo@0.1.0",
   "nodes":{  
      "1":{  
         "id":1,
         "data":{"num":1},
         "inputs":[],
         "outputs":[  
            {"connections":[  
                  {"node":3, "input":0 }
            ]}
         ],
         "position":[ 80, 200 ],
         "name":"Number"
      }
   }
}
```

With the same success you can restore the contents of the editor using this data (provided that the versions of your data and the editor are the same)

```js
await editor.fromJSON(data);
```

In the case of different identifiers in `data` and the `editor`, the method `fromJSON` throws an exception. You must catch it and notify the user or try to fit the data to a new version of your editor
