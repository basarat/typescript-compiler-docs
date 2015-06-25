# Sections 
The main https://github.com/Microsoft/TypeScript/tree/master/src/compiler

scanner -> parser -> (binder,checker)  -> emitter 

`parser.ts` / `createProgram` (and then the program) seems to be the main orchestrator in the compiler.

# CURRENT
## Parser
We have a single parser (as a module `ts.Parser`) as a namespace module. This gets reinitilized using `initializeState` function which also resets the internal *single `const`* `scanner`. 

## Scanner
We have a single scanner `const scanner = createScanner(ScriptTarget.Latest, /*skipTrivia*/ true);` in the parser that is shared (for performance) and muated using `setText` and `setScriptTarget`.


# LEGACY

# Call Order

`tc.ts` `executeCommandLine` (also inside this function `parseCommandLine` from `commandLineParser.ts`) 

will call `createProgram` in parser.

## createProgram (parser)
`createProgram` in `parser.ts` The filenames from the command-line are called `rootNames` here. 

The parser initiates the scanner on each of the files in `rootName` + `lib.d.ts`, using `processRootFile` (> `processSourceFile` > `findSourceFile` > `host.getSourceFile`(which is inside `ts.ts` based on `host` and used to simply read the content) > calls back into `createSourceFile` inside the parser. 


## createSourceFile (parser)
`createSourceFile` in the parser will create the scanner on the found text using `createScanner`. Also it has a bunch of utility (nested) functions e.g. `processReferenceComments`

## createScanner (scanner)
Creates the scanner. A new one is created for each file that needs to be scanned (including `lib.d.ts`)

## emitFiles (emitter)
The main function for emiting the output. Called from the `checker` (which itself is a part of the `program`). 
See `tc.ts` : `program.getTypeChecker().emitFiles()` (an alias to `invokeEmitter` in `checker.ts` which simply calls `emitFiles` in `emitter.ts`)


# Type Checker
## createTypeChecker 
Called from `parser` specifically `createProgram`, and stored on the `program` (`getTypeChecker`).

Does its work on a call to `checker.getDiagnostics` (called from `tc.ts`)
