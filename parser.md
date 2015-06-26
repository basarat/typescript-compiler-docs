# Parser

> Starting from a set of sources, and following the productions of the language grammar, to generate an Abstract Syntax Tree (AST).

We have a single parser (as a module `ts.Parser`) as a namespace module. This gets reinitilized using `initializeState` function which also resets the internal *single `const`* `scanner`. 

## Entry
Main entry point into the parser is `createSourceFile` (called from `program`) 

This in turn calls `parseSourceFile` which in turn just initializes the scanner and works through (local function `parseSourceFileWorker`) the file contents.

## Managing the scanner
`initializeState` resets the scanner (and does other cleanup).


## The heart of the parser
`parseSourceFileWorker` has the following line (notice `parseStatement`): 

```ts
sourceFile.statements = parseList(ParsingContext.SourceElements, parseStatement);
```

This starts the main recursive decent into the syntax tree.

There are a bunch of `parse*` functions (e.g. `parseSwitchStatement`) that do the heavy lifting. They all lead up to `parseStatement` at some point. Many of these call `parseStatement` themselves.


# DEMO
## PR for JSX / `as`
https://github.com/Microsoft/TypeScript/pull/3564 
Specifically : https://github.com/RyanCavanaugh/TypeScript/commit/556cb70c1d0de4d450baadf48279b7f0ca3d954e and https://github.com/RyanCavanaugh/TypeScript/commit/a4045e539b4f115f854d3cb3351089e62a59f3f0
