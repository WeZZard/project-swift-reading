# Reading TypeLowering.h

## Referred By

include/swift/SIL/SILModule.h

include/swift/SIL/SILWitnessVisitor.h

lib/SILGen/SILGen.h

lib/TBDGen/TBDGenVisitor.h [^1]

[How to talk to your kids about SIL type use](https://medium.com/@slavapestov/how-to-talk-to-your-kids-about-sil-type-use-6b45f7595f43) (Refers the implementation file `lib/SIL/TypeLowering.cpp`)

## Outline

### namespace swift

### namespace swift::Lowering

- adjustFunctionType series functions

- IsTrivial_t
- IsFixedABI_t
- IsAddressOnly_t
- IsReferenceCounted_t
- IsResillent_t
- IsTypeExpansionSensitive_t
- IsInfiniteType_t

- class TypeLowering
- struct SILConstantInfo
	- Type and lowering information about a constant function
- enum class CaptureKind
- class TypeConverter
	- Helper class for creating and managing type lowerings
- enum class DifferentiationThunkKind

### namespace llvm

struct DenseMapInfo<swift::Lowering::TypeConverter::CachingTypeKey>

struct DenseMapInfo<swift::Lowering::TypeConverter::OverrideKey>

## class TypeLowering

A pure abstract class which stores detailed informations about a type lowering from Swift type to SIL type.

### Subclasses



### Member Variables

- LoweredType : SILType
- Properties : RecursiveProperties
- expansionContext : TypeExpansionContext
- ReferenceCounted : unsigned ~ 1
- NextExpansion : mutable const TypeLowering * = nullptr

### Functionalities

- Stores lowered SILType instance
- Accessing `RecursiveProperties`
- Accessing `TypeExpansionContext`
- SIL emission
	- load of copy
	- store of copy
	- store
	- load
	- copy into
	- destroy address
	- destroy r-value
	- lowered load
	- lowered store
	- lowered destroy value
	- lowered destroy child value
	-  lowered destroy value direct children
	-  lowered destroy value most derived descendants
	-  destroy value
	-  lowered copy value
	-  lowered copy value direct children
	-  lowered copy value most derived descendants
	-  copy value
	-  lowered copy child value
- new & delete with `TypeConverter`’s allocator

## class TypeConverter

### Subtypes

- struct CachingTypeKey
- struct TypeKey
- struct OverrideKey
- LowerAggregateTypeRAII
- GenericContextRAII
- BridgedTypePurpose
- LoweredFormalTypes
- ABIDifference

### Memeber Variables

<private>

TypeLoweringBPA : llvm::BumpPtrAllocator; // The bump pointer allocator

CurGenericSignature : CanGenericSignature;

AggregateFieldBeingLowered  : {[CanType]};

LoweredTypes : [const TypeLowering * : CacheTypeKey]

ConstantTypes : [(TypeExpansionContext , SILDeclRef) : SILConstantInfo *]

ConstantOverrideTypes : [OverrideKey : SILConstantInfo *]

LoweredCaptures : [SILDeclRef : CaptureInfo]

TypeFields : [(SILType, unsigned) : unsigned]

ClosureAbstractionPatterns : [AbstractClosureExpr * :  AbstractionPattern?]

CaptureTypeExpansionContext : [SILDeclRef : TypeExpansionContext]

Optional CanType storages for bridge types defines in `swift/SIL/BridgedTypes.def`

<public>

M : ModuleDecl &

Context : ASTContext &

### Functionality Groups

key-value management: `TypeKey` for `TypeLowering`

Nested aggregated type lowering

Nested generic context

Getting type lowering

Getting lowered SIL type

Getting lowered r-value type

Getting abstraction pattern



- - -

[^1]: TBD:  text-based dynamic library stub file format