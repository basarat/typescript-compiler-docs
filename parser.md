# Parser

> Starting from a set of sources, and following the productions of the language grammar, to generate an Abstract Syntax Tree (AST).

We have a single parser (as a module `ts.Parser`) as a namespace module. This gets reinitilized using `initializeState` function which also resets the internal *single `const`* `scanner`. 


## Managing the scanner
`initializeState` resets the scanner (and does other cleanup).
