# Reading emitImplicitValueConstructor

## Prototype

`emitImplicitValueConstructor(SILGenFunction &, ConstructorDecl *)`

## Process

- Emit the indirect return argument, if any
- Emit the elementwise arguments.
- Emit constructor metatype arg
- If there is indirect return:
	- initialize it in-place.
- Else:
	- build a struct value directly from the elements.