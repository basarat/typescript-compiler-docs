typescript-compiler-docs
========================

*UPDATE* : we have offical docs now! : https://github.com/Microsoft/TypeScript/wiki/Using-the-Compiler-API 

A work in progress documentation of how the TypeScript compiler works. 

# Running tests
Best way I have found is to : 
```
$ ./node_modules/mocha/bin/mocha -R dot -g nodeModules --colors  -t 20000 built/local/run.js
```
The official greping `jake runtests test=nodeModules` didn't work (though `test=project` does work). 

# Useful PRs with Insight

## Missing try block with a catch / finally 
https://github.com/Microsoft/TypeScript/pull/262/files
**Parser** : Gives information about the `isStatement` function. If that function returns then `parseStatement` will get called which must return *some* type of `Statement`. In this case we are going to return a `TryStatement` from a utility `parseCatchOrFinallyBlocksMissingTryStatement` function. These utility (`parse-`) functions apparently prepare the AST for the emitter by creating (`createNode` function) and finishing Nodes (`finishNode` function).

## Parsing logic for Object Literals 
https://github.com/Microsoft/TypeScript/pull/273/files
**Parser** : `parseObjectLiteral` is used to parse object literals. `parseDelimitedList` is used to parse the properties of object literals.

## Reference tag lookup
https://github.com/Microsoft/TypeScript/pull/365/files Changed to load extensionless reference tags (`.d.ts` and `.ts`)

## Fast compilation on watch
https://github.com/Microsoft/TypeScript/pull/324
Basically create a copy of the `program` and update it based on changes. Mostly inside `tc.ts` the `recompile` function.


## Language Service 

### Initial port of the language service
https://github.com/Microsoft/TypeScript/pull/303

### Pull model for contextual types 
https://github.com/Microsoft/TypeScript/pull/330
`getContextualType` function by anders

# Emitter
## The class emit
https://github.com/Microsoft/TypeScript/pull/331/files 
change the order of `this` emit in class body. `emitClassDeclaration`

## Emitter / checker for `.d.ts` files 
https://github.com/Microsoft/TypeScript/pull/414 
Declaration file needs to always emit typeof function/static function instead of emitting signature

## File extension for emit different from '.js'
Ability to modify the output filename extension https://github.com/Microsoft/TypeScript/pull/425

# Checker
## Change the order in which the functions are checked in overloads
https://github.com/Microsoft/TypeScript/pull/378 
```ts
 interface A { (x: string): void }
 interface B { (x: 'foo'): string }
 var b: B;
 b('foo') // <- here overloads should be processed as [(x:'foo'): string, (x: string): void]
```

# Tutorials 
**hacking it to add a documentation emitter** http://www.codeproject.com/Articles/635280/TypeScript-Compiler-Documentation-Output 


## More language service 
An overview of a file in terms of lexical structure : https://github.com/Microsoft/TypeScript/issues/534

# Various Classifer
https://github.com/Microsoft/TypeScript/issues/1477#issuecomment-66904683 

# Walking the syntax Tree

Uses the `ts.forEachChild` exported from the `parser`. Helpful information from [this comment](https://github.com/Microsoft/TypeScript/issues/254#issuecomment-68940929). Note how the `Node` is augmented by `services` [here](https://github.com/Microsoft/TypeScript/blob/6f6c46a99f446144702bb324f6b50d94a000a690/src/services/services.ts#L14)

Demo : http://blog.ctaggart.com/2015/01/typescript-14-ast-from-nodejs.html

# Things on nodes only get written once 
https://github.com/Microsoft/TypeScript/issues/1514#issuecomment-69105512 

# Some Terminal nodes are not in the AST 
They can be "rehydrated" on demand using the sourcetext by calling `getChildren()` https://github.com/Microsoft/TypeScript/issues/1514#issuecomment-69118975 

# Formatting your documents 
In the language service https://github.com/Microsoft/TypeScript/issues/1651#issuecomment-69684066 
* `getFormattingEditsForRange`
* `getFormattingEditsAfterKeystroke`
* `getFormattingEditsForDocument`
  for `getFormattingEditsForDocument` once you've gotten the appropriate edits ranges, you can easily apply them in reverse and fix up the original source text.

Once you have them you can apply them in reverse : https://github.com/Microsoft/TypeScript/issues/1651#issuecomment-69877863 
```ts
function formatCode(orig: string, changes: TextChange[]): string {
    var result = orig;

    for (var i = changes.length - 1; i >= 0; i--) {
        var change = changes[i];
        var head = result.slice(0, change.span.start);
        var tail = result.slice(change.span.start + change.span.length)
        result = head + change.newText + tail;
    }

    return result;
}
```

Demo : http://blog.ctaggart.com/2015/01/format-typescript-with-v14-language.html
There is also smart indentation alone: https://github.com/Microsoft/TypeScript/issues/1754

# Support JSX 
You can convert JSX to JavaScript using the API from : https://www.npmjs.com/package/react-tools 
JSX Specification : http://facebook.github.io/jsx/
How to augment: https://github.com/facebook/react/issues/759#issuecomment-50529015

# Skipped tokens by the new foreachchild AST traversal
https://github.com/Microsoft/TypeScript/issues/1728#issuecomment-70592200 

# Program vs. Language Service
`languageService` and `program` are very similar in principal. Which you use depends on your scenario.

`program` is great if you just want emit / linting, the os related features can be provided with a `compilerHost` generated with a simple call to `createCompilerHost`.  Examples : https://github.com/Microsoft/TypeScript/wiki/Using-the-Compiler-API#a-minimal-compiler and https://github.com/Microsoft/TypeScript/wiki/Using-the-Compiler-API#traversing-the-ast-with-a-little-linter

`languageService` is great if want something like a full blown IDE support. 

More on the difference: 

https://github.com/Microsoft/TypeScript/issues/1786#issuecomment-71567975 

> a singe LanguageService object does not map to a single Program object, it actually maps to a set of them. A Program is an immutable object, the LanguageService on the other hand handles changes. under the covers, the LanguageService, on every change, creates a new Program instance to map to the current context, but uses the previous Program instance's unchanged SourceFiles to save computation. If the LanguageService was just an extension over Program, you would need to create a new LanguageService object on every change.


# Expand the list of files knows by language service 
`LanguageService.getProgram().getSourceFiles()` More https://github.com/Microsoft/TypeScript/commit/9628191a1476bc0dbdb28bfd30b840656ffc26a3#commitcomment-9490325

# Langauge service is just a wrapper on `program` and a thin one : 

e.g. [`getQuickInfoAtPosition`](https://github.com/Microsoft/TypeScript/blob/master/src/services/services.ts#L3195) and [`getCompletionsAtPosition`](https://github.com/Microsoft/TypeScript/blob/master/src/services/services.ts#L2311) https://github.com/Microsoft/TypeScript/issues/2105#issuecomment-75417304


# Useful blog post on using `program` 
http://www.scottlogic.com/blog/2015/01/20/typescript-compiler-api.html


# Why the compielr AST is different from Mozilla AST 
https://github.com/Microsoft/TypeScript/issues/1690#issuecomment-77713066 
