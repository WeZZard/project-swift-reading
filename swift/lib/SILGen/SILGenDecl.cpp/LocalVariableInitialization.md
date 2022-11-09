# Reading LocalVariableInitialization

## Summary

An RAII object for SIL emission of a local variable.

## Member Variables

`decl : VarDecl *`

`Box : SILValue`

`Addr : SILValue`

`DeallocCleanup : CleanupHandle`

`ReleaseCleanup : CleanupHandle`

`DidFinish bool = false`

## Constructor

The constructor get the context box type (`boxType`) for type for capturing `VarDecl *` with lowered  `VarDecl *`’s type and generic environment which marked as `mutable`
- `CanSILBoxType TypeConverter::getContextBoxTypeForCapture`
- `CanType TypeConverter::getLoweredRValueType`
- `GenericEnvironment * SILFunction::getGenericEnvironment`
- `typedef CanTypeWrapper<SILBoxType> CanSILBoxType`

The constructor emits `alloc_box` with `boxType` and sets to `Box`
- `SILGenBuilder::createAllocBox`

The constructor emits `mark_unintialized` if there is uninitialized kind (`kind`)  and sets to `Box`
- `SILGenBuilder::createMarkUninitialized`

The constructor emits `begin_borrow` and sets to `Box` if `supportsLexicalLifetimes` is enabled for the SIL module
* `SILGenBuilder::createBeginBorrow`

The constructor emits `project_box` and sets to `Addr`
- `SILGenBuilder::createProjectBox`

The constructor sets `ReleaseCleanup`

The constructor sets `DeallocCleanup`

## Destructor

Checks `DidFinish`