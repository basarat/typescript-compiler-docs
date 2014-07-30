# Sections 
The main https://github.com/Microsoft/TypeScript/tree/master/src/compiler

scanner -> parser -> (binder,checker)  -> emitter 

# Call Order

`executeCommandLine` (also inside `parseCommandLine` from `commandLineParser.ts`) 
> `createProgram` the parsed filenames are called `rootNames` here
> 
