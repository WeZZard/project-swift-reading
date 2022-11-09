# Reading class DeclName in Identifier.h

## Subtypes

- `class CompoundDeclName`

## Member

- `BaseNameOrCompound : llvm::PointerUnion<DeclBaseName, CompoundDeclName *>`

## Construction

- `DeclName()` : build null `DeclName`.

- `DeclName(DeclBaseName)`: Build a simple value name with one
  component.

- `DeclName(Identifier)`

- `DeclName(ASTContext &, DeclBaseName, ArrayRef<Identifier>)`: Build a
  compound value name given a base name and a set of argument names.

- `DeclName(ASTContext &, DeclBaseName, ParameterList *)`: Build a
  compound value name given a base name and a set of argument names
  extracted from a parameter list.

