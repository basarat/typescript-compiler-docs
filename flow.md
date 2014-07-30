# Sections 
The main https://github.com/Microsoft/TypeScript/tree/master/src/compiler

scanner -> parser -> (binder,checker)  -> emitter 

# Call Order

`tc.ts` `executeCommandLine` (also inside this function `parseCommandLine` from `commandLineParser.ts`) 
--- `createProgram` in `parser.ts` 

The parser 

> 
