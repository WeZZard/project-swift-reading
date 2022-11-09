# TypeConverter

## getContextBoxTypeForCapture

#### Prototype

```cpp
CanSILBoxType getContextBoxTypeForCapture(ValueDecl *captured, CanType loweredContextType, GenericEnvironment *env, bool isMutable)
```

#### Process

DECLARE `loweredInterfaceType` : `CanType` <- `loweredContexctType`

IF has `env : GenericEnvironment *`:
	EVALUATE `r%declCtx` <- `captured` -> `getDeclContext()`
	EVALUATE `homeSig` <- `r%declCtx` -> `getGenericSignatureOfContext()`
	EVALUATE `lowredInterfaceType`<- `lowredInterfaceType` -> `mapTypeOutOfContext()` -> `getCanonicalType(homeSig)`

DECLARE `boxType` <- `getInterfaceBoxTypeForCapture(captured, loweredInterfaceType)`

IF has `env`:
	`boxType` <- `env` -> `mapTypeIntoContext(boxType)` -> `getCanonicalType()`

RETURN `boxType`

## getInterfaceBoxTypeForCapture

#### Prototype

```cpp
CanSILBoxType getInterfaceBoxTypeForCapture(ValueDecl *captured, CanType loweredInterfaceType, bool isMutable)
```

#### Process

DECLARE `C` : `ASTContext&` <- `M : SILModule &` -> `getASTContext()`

DECLARE `r%declCtx` <- `captured` -> `getDeclContext()`

DECLARE `r%genericSigCtx` <- `r%declCtx` -> `getGenericSignatureOfContext()`

DECLARE `signature` <- `getCanonicalSignatureOrNull(r%genericSigCtx)`

IF `loweredInterfaceType` has no type parameter:
	DECLARE `layout` <- `SILLayout::get(...)` with `loweredInterfaceType`
	RETURN `SILBoxType::get(...)` with `layout`

…

#### Conclusion

The `SILLayout` is generated with `loweredInterfaceType`.

## getCanonicalSignatureOrNull

## getLoweredRValueType

DISPATCH TO `getLoweredType(...).getASTType()`

## getLoweredType

DISPATCH TO `getTypeLowering(...).getLoweredType()`

## getTypeLowering

## Terminologies

`GenericEnvironment`: Describes the mapping between archetypes and interface types for the generic parameters of a `DeclContext`.
