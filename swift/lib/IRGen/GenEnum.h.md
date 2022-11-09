# Reading GenEnum.h

## Outline

- emitSwitchAddressOnlyEnumDispatch
- emitInjectLoadableEnum
- emitProjectLoadableEnum
- emitProjectEnumAddressForStore
- emitDestructiveProjectEnumAddressForLoad
- emitStoreEnumTagToAddress
- emitGatherBits
- gatherBits
- emitScatterBits
- scatterBits
- EnumImplStrategy

## EnumImplStrategy::Element

Stores:
- `EnumElementDecl *` for the element’s decl
- a `TypeInfo *` for the type info of the element
- a`TypeInfo *` for the original type info of the element.

## EnumImplStrategy::TypeInfoKind

An enum of 3 cases:

- Opaque, indicates that the enum has a `NonFixedTypeInfo`
- Fixed, indicates that the enum has a `FixedTypeInfo`
- Loadable, indicates that the enum has a `LoadableTypeInfo`

## EnumImplStrategy

### Member Variables

ElementsWithPayload : [Element]

ElementsWithNoPayload : [Element]

IGM : IRGenModule&

TI : const TypeInfo * = nullptr

TIK : TypeInfoKind

AlwaysFixedSize : IsFixedSize_t

ElementsAreABIAccessible : IsABIAccessible_t

NumElements : unsigned

### Member Function

#### Utilities

- Save and return the `TypeInfo` created for the enum.

`TypeInfo *registerEnumTypeInfo(TypeInfo *mutableTI)`

- Constructs a TypeInfo for an enum of the best possible kind for its layout, FixedEnumTypeInfo or LoadableEnumTypeInfo.

`TypeInfo *getFixedEnumTypeInfo(llvm::StructType *T, Size S, SpareBitVector SB, Alignment A, IsPOD_t isPOD, IsBitwiseTakable_t isBT);`

##### Factory Method

`static std::unique_ptr<EnumImplStrategy> get(TypeConverter &TC, SILType Type, EnumDecl *theEnum);`

#### Completing Enum Type Layout

`virtual TypeInfo *completeEnumTypeLayout(TypeConverter &TC, SILType Type, EnumDecl *theEnum, llvm::StructType *enumTy)`

#### Getting Enum Type Layout Info

const TypeInfo &getTypeInfo()

llvm::StructType *getStorageType()

IsPOD_t isPOD

#### Query enum layout

[Element] getElementsWithPayload()

[Element] getElementsWithNoPayload()

unsigned getTagIndex

*virtual* getDiscriminatorIndex

*virtual* isReflectable

*virtual* getTagBitsForPayloads

*virtual* getBitPatternForNoPayloadElement

*virtual* getBitMaskForNoPayloadElements()

#### Indirect enum operations

emitIndirectCaseTest

emitIndirectSwitch

emitExtractDiscriminator

#### Loadable enum operations

emitValueInjection

emitValueCaseTest

emitValueSwitch

emitValueProject

#### Delegated TypeInfo operations

addToAggLowering

#### Delegated FixedTypeInfo operations

#### Delegated LoadableTypeInfo operations