# Reading SILLayout.h

## Concepts
`SILLayout` describes the physical layout of nominal types in SIL, including structs, classes, and boxes. This is distinct from the AST-level layout for several reasons:
- It avoids redundant work lowering the layout of aggregates from the AST.
- It allows optimizations to manipulate the layout of aggregates without requiring changes to the AST. For instance, optimizations can eliminate dead fields from instances or turn invariant fields into global variables.
- It allows for SIL-only aggregates to exist, such as boxes.
- It improves the robustness of code in the face of resilience. A resilient type can be modeled in SIL as not having a layout at all, preventing the inappropriate use of fragile projection and injection operations on the type.

## SILField

```cpp
enum : unsigned {
  IsMutable = 0x1,
};
  
static constexpr const unsigned NumFlags = 1;
```

`SILField` stores an `llvm::PointerIntPair` of `CanType` and `unsigned` of `NumFlags` width

## SILLayout

`SILLayout` is a folding set node.

`SILLayout` has a sequence of `SILField` as trailing objects.

> Folding set node and trailing object is a human understood concept in LLVM.



