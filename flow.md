# Sections 
The main https://github.com/Microsoft/TypeScript/tree/master/src/compiler

scanner -> parser -> (binder,checker)  -> emitter 

# Call Order

`executeCommandLine` (also inside `parseCommandLine` in `commandLineParser.ts`) 
> `createProgram`
> 
