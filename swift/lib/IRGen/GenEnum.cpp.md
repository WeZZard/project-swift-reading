# Reading GenEnum.cpp

## Class Hierarchy of EnumImplStrategy

- EnumImplStrategy
	- SingletonEnumImplStrategy
	- PayloadEnumImplStrategyBase
		- SinglePayloadEnumImplStrategy
		- MultiPayloadEnumImplStrategy
	- ResilientEnumImplStrategy
- NoPayloadEnumImplStrategy
	- Inherited from SingleScalarTypeInfo
		- template args
			- Derived <- NoPayloadEnumImplStrategyBase
			- Base <- EnumImplStrategy
	- CCompatibleEnumImplStrategy

## SinglePayloadEnumImplStrategy

### Inherited Member Variables

<PayloadEnumImplStrategyBase>

 protected:

PayloadSchema : EnumPayloadSchema

PayloadElementCount : unsigned

ExtraTagTy : llvm::IntegerType *

PayloadBitCount : unsigned

ExtraTagBitCount : unsigned = ~0U

NumExtraTagValues : unsigned = ~0U

### Member Variables

CopyDestroyKind : CopyDestroyStrategy

Refcounting : ReferenceCounting

NumExtraInhabitantTagValues : unsigned = ~0U

loweredType : SILType

copyEnumFunction : mutable llvm::Function * = nullptr

consumeEnumFunction : mutable llvm::Function * = nullptr

PayloadTypesAndTagType : [llvm::Type *]

### completeEnumTypeLayout

IF `TIK` >= `Fixed`
	RETURN `completeFixedLayout`
RETURN `completeDynamicLayout`

### completeFixedLayout



### completeDynamicLayout

## MultiPayloadEnumImplStrategy

## ResilientEnumImplStrategy