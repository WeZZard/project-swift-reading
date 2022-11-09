# Reading class DeclName::CompoundDeclName in Identifier.h

## Inheritance

- public `llvm::FoldingSetNode`

- private `llvm::TrailingObjects<CompoundDeclName, Identifier>`

## Members

- `BaseName : DeclBaseName`
- `NumArgs : size_t`

## Construction

- `CompoundDeclName(DeclBaseName, size_t)`

## Read

- `ArrayRef<Identifier> getArgumentNames()`
  Reads trailing `Identifier`s with an `ArrayRef<Identifier>`.

## Update

- `MutableArrayRef<Identifier> getArgumentNames()`
  Updates trailing `Identifier`s with a `MutableArrayRef<Identifier>`.

