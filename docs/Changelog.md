Changelog
=

## v1.0.0-alpha.7

- ### Replaced indexes of inputs/outputs with a keys [#124](https://github.com/retejs/rete/issues/124)

builder():
| Before | Now |
| --- | --- |
| `new Input('Title', socket)` | `new Input('key', 'Title', socket)` |
| `new Output('Title', socket)` | `new Output('key', 'Title', socket)` |

worker():
| Before | Now |
| --- | --- |
| `inputs[0][0]` | `inputs['key'][0]` |
| `outputs[0]` | `outputs['key']` |

Keys in outputs or inputs list must be unique


<span style="color:red">Warning!</span> Data from previous versions incompatible with current version.

- ### Fixed zoom in Firefox/IE [#139](https://github.com/retejs/rete/issues/139)