typescript-compiler-docs
========================

*UPDATE* : we have offical docs now! : https://github.com/Microsoft/TypeScript/wiki/Using-the-Compiler-API 

A work in progress documentation of how the TypeScript compiler works. 

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


# Things on nodes only get written once 
https://github.com/Microsoft/TypeScript/issues/1514#issuecomment-69105512 

# Some Terminal nodes are not in the AST 
They can be "rehydrated" on demand using the sourcetext by calling `getChildren()` https://github.com/Microsoft/TypeScript/issues/1514#issuecomment-69118975 
