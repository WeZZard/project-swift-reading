# Reading class DeclBaseName in Identifier.h

## Member

- `Ident : Identifier`

## Constructor

- `DeclBaseName()`
  Creates a decl base name with empty identifier.

- `DeclBaseName(Identifier)`
  Creates a decl base name with an identifier.

- `static DeclBaseName::createSubscript()`
  Creates a subscript decl base name.

- `static DeclBaseName::createConstructor()`
  Creates a constructor decl base name.

- `static DeclBaseName::createDestructor()`
  Creates a destructor decl base name.
