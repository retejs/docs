Controls
=

Controls are necessary in order to allow you to expand the functionality of the nodes. You can insert any html template and a third-party object (input, select, image, jQuery plugin etc). 

By default, all controls are displayed using AlightRenderPlugin, but you can create and display controls in any other way, using interfaces for plug-ins

```js
class NumControl extends Rete.Control {

    constructor(emitter) {
        super();
        this.emitter = emitter;
        this.template = '<input type="number" :value="value" @input="change($event)"/>';

        this.scope = {
            value: 0,
            change: this.change.bind(this)
        };
    }

    change(e){
        this.scope.value = +e.target.value;
        this.update();
    }

    update(){
        this.putData('num', this.scope.value)
        this.emitter.trigger('process');
        this._alight.scan();
    }

    mounted() {
        this.scope.value = this.getData('num') || 0;
        this.update();
    }

    setValue(val) {
        this.scope.value = val;
        this._alight.scan()
    }
}
```

As you can see, the controls can not only display some information, but also change it and store it in the node for further processing. 

In this case, the control puts a number in the node data. It can be used when there is no connection on the input.

