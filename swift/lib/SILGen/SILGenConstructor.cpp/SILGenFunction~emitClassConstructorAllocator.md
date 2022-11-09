# Reading SILGenFunction::emitClassConstructorAllocator

## Summary
This function emits class constructor allocator.

Swift object adopted two-step construction which means firstly there is an allocation then there is initialization. This behavior is reflected in this function:

**First**, the function emits a call to “allocation”:

For constructor decl has clang node or uses ObjC dispatch or is convenience init, it emits `alloc_ref_dynamic`; Otherwise, it emits `alloc_ref`.

**Then**, the function emits a function ref for the initializer. (This is because that the function may be in other SIL module?)

For initializers uses Objective-C dispatch, a dynamic method ref would be emitted; Otherwise a global function ref.

**Third**, the function emits an “apply with rethrow” to the initializer with the previously emitted function ref.

## Prototype

`void SILGenFunction::emitClassConstructorAllocator(ConstructorDecl *ctor)`

## Process

Creating a SIL location (`SILLocation`) and mark as auto-generated.

Declare `args` as `[SILValue]`

`args` <- Bind parameters for forwarding via `SILGenFunction::bindParametersForForwarding`

`selfMetaValue` <- emit constructor metatype arg via `SILGenFunction::emitConstructorMetatypeArg`

Allocate `self` value

If `ctor`: has clang node? should use ObjC dispatch? is convenience init?
	`allocArg` <- `selfMetaValue`
	If `useObjCAllocator`:
		`allocArg` <- create thick to ObjC metatype via `SILGenBuilder::createThickToOBjCMetatype`
Create `alloc_ref_dynamic` via `SILGenBuilder::createAllocRefDynamic`

Else:
	Create `alloc_ref`
- `SILGenBuilder::createAllocRef`

`args` <- `selfValue`

(`initVal`, `initTy`) <- emit sibling method ref
- `SILGenFunction::emitSiblingMethodRef`

`initedSelfValue` <- emit apply with rethrow for `initVal`, `initTy`
- `SILGenFunction::emitApplyWithRethrow`

Emit profiler increment
- `SILGenFunction::emitProfilerIncrement`

Create return
- `SILGenBuilder::createReturn`
