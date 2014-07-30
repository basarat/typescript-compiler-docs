# Sections 
The main https://github.com/Microsoft/TypeScript/tree/master/src/compiler

scanner -> parser -> (binder,checker)  -> emitter 

# Call Order

`tc.ts` `executeCommandLine` (also inside this function `parseCommandLine` from `commandLineParser.ts`) 


`createProgram` in `parser.ts` The filenames from the command-line are called `rootNames` here. 

The parser initiates the scanner on each of the files in `rootName` + `lib.d.ts`, using `processRootFile`


