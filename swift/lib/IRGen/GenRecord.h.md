# Reading GenRecord.h

Provides some common code for emitting record types. A record type is
something like a tuple or a struct.

## Outline

- ![class RecordField](#RecordField)
  - template arg
    - FieldImpl
- enum FieldsAreABIAccessible_t: bool
- ![class RecordTypeInfoImpl](#RecordTypeInfoImpl)
  - template args
    - class `Impl`
    - class `Base`
    - class `FieldImpl_`
    - bool `IsLoadable` = std::is_base_of<LoadableTypeInfo, Base>::value
  - Inherits `Base`
  - Inherits `llvm::TrailingObjects<Impl, FieldImpl_>`
- _forward declaration_ ![class RecordTypeInfo](#RecordTypeInfo-Series-Type)
  - template args
    - class Impl
    - class Base
    - class FieldImpl_
    - bool `IsFixedSize` = std::is_base_of<FixedTypeInfo, Base>::value
    - bool `IsLoadable` = std::is_base_of<LoadableTypeInfo, Base>::value
- ![class RecordTypeInfo](#RecordTypeInfo-where-IsFixedSize=false,-IsLoadable=false)
  - template args
    - class Impl
    - class Base
    - class FieldImpl_
    - bool `IsFixedSize` = false
    - bool `IsLoadable` = false
  - Inherits RecordTypeInfoImpl<Impl, Base, FieldImpl>
- ![class RecordTypeInfo](#RecordTypeInfo-where-IsFixedSize=true,-IsLoadable=false)
  - template args
    - class Impl
    - class Base
    - class FieldImpl_
    - bool `IsFixedSize` = true
    - bool `IsLoadable` = false
  - Inherits RecordTypeInfoImpl<Impl, Base, FieldImpl>
- ![class RecordTypeInfo](#RecordTypeInfo-where-IsFixedSize=true,-IsLoadable=false)
  - template args
    - class Impl
    - class Base
    - class FieldImpl_
    - bool `IsFixedSize` = true
    - bool `IsLoadable` = true
  - Inherits RecordTypeInfo<Impl, Base, FieldImpl, true, false>
- ![class RecordTypeBuilder](#RecordTypeBuilder)
  - template args
    - class BuilderImpl
    - class FieldImpl
    - case ASTField

## Terminologies

### ABI Accessible

Accessible without value witness function

### Extra Inhabitants

> …, that is, bit patterns with the size and alignment of the type but which
> do not form valid values of that type, they are used to represent the
> no-data cases, with extra inhabitants in order of ascending numeric value
> matching no-data cases in declaration order.

## RecordField

### Member Variables

- ElementLayout Layout;
- unsigned Begin : 16;
- unsigned End : 16;

> the range of explosion indexes for this element

### Functionalities

- Getting type info from element layout
- Complete from element layout
  - void completeFrom(const ElementLayout &layout)
- Getting layout info
  - is empty
  - is POD
  - is ABI accessible
  - kind (`ElementLayout::Kind`)
  - has fixed byte offset
  - fixed byte offset
  - struct index
  - non fixed element index
  - projection range
- Projecting address

### Conclusion

A composited type of `ElementLayout` and the projection range of a record
field.

## RecordTypeInfoImpl

> A metaprogrammed TypeInfo implementation for record types.

The `Base` template arguments is another *`TypeInfo` class.

### Member Variables

- `const unsigned NumFields`
- `const unsigned AreFieldsABIAccessible : 1`
- `mutable Optional<const FieldImpl *> ExtraInhabitantProvidingField`
- `mutable Optional<bool> MayHaveExtraInhabitants`

### Trailing Objects

- A vector of `FieldImpl`

Copied from `[FieldImpl]` when the instance under construction.

### Functionalities

- Creation

```cpp
template <class... As>
  static Impl *create(ArrayRef<FieldImpl> fields, As &&...args)
```

- Getting fields info

```cpp
bool areFieldsABIAccessible()
```

- Getting fields

```cpp
ArrayRef<FieldImpl> getFields()
```

- Getting explosion schema (`ExplosionSchema`)

```cpp
void getSchema(ExplosionSchema &schema) const override
```

- Emitting value witness function call

```cpp
void assignWithCopy(IRGenFunction &IGF, Address dest, Address src, SILType T,
                    bool isOutlined) const override

void assignWithTake(IRGenFunction &IGF, Address dest, Address src, SILType T,
                    bool isOutlined) const override

void initializeWithCopy(IRGenFunction &IGF, Address dest, Address src,
                        SILType T, bool isOutlined) const override

void initializeWithTake(IRGenFunction &IGF, Address dest, Address src,
                        SILType T, bool isOutlined) const override

void destroy(IRGenFunction &IGF, Address addr, SILType T,
            bool isOutlined) const override

bool mayHaveExtraInhabitants(IRGenModule &IGM) const override
```

- Perform an operation using the field that provides extra inhabitants for
the aggregate, whether that field is known statically or dynamically.

```cpp
llvm::Value *withExtraInhabitantProvidingField(IRGenFunction &IGF,
  Address structAddr,
  SILType structType,
  llvm::Value *knownStructNumXI,
  llvm::Type *resultTy,
  llvm::function_ref<llvm::Value* (const FieldImpl &field, llvm::Value *numXI)> body) const
```

- Getting extra inhabitants providing field

```cpp
const FieldImpl *
getFixedExtraInhabitantProvidingField(IRGenModule &IGM) const
```

- Collecting metadata for outlining

```cpp
void collectMetadataForOutlining(OutliningMetadataCollector &collector,
                                SILType T) const override
```

## RecordTypeInfo Series Type

### Implementations

- non-fixed-size types (but not resilient ones where we don't know the
complete set of stored properties)
- fixed-layout types that aren't necessarily loadable.
- loadable types.

## RecordTypeInfo where IsFixedSize=false, IsLoadable=false

```cpp
Address initializeBufferWithCopyOfBuffer(IRGenFunction &IGF,
                                         Address destBuffer,
                                         Address srcBuffer,
                                         SILType type) const override
```

## RecordTypeInfo where IsFixedSize=true, IsLoadable=false

```cpp
unsigned getFixedExtraInhabitantCount(IRGenModule &IGM) const override

bool canValueWitnessExtraInhabitantsUpTo(IRGenModule &IGM,
                                        unsigned index) const override

APInt getFixedExtraInhabitantValue(IRGenModule &IGM,
                                  unsigned bits,
                                  unsigned index) const override

APInt getFixedExtraInhabitantMask(IRGenModule &IGM) const override

llvm::Value *getExtraInhabitantIndex(IRGenFunction &IGF,
                                    Address structAddr,
                                    SILType structType,
                                    bool isOutlined) const override

void storeExtraInhabitant(IRGenFunction &IGF,
                          llvm::Value *index,
                          Address structAddr,
                          SILType structType,
                          bool isOutlined) const override

```

## RecordTypeInfo where IsFixedSize=true, IsLoadable=true

```cpp
void loadAsCopy(IRGenFunction &IGF, Address addr,
                Explosion &out) const override

void loadAsTake(IRGenFunction &IGF, Address addr,
                Explosion &out) const override

void assign(IRGenFunction &IGF, Explosion &e, Address addr,
              bool isOutlined) const override

void initialize(IRGenFunction &IGF, Explosion &e, Address addr,
                  bool isOutlined) const override

unsigned getExplosionSize() const override

void reexplode(IRGenFunction &IGF, Explosion &src,
              Explosion &dest) const override

void copy(IRGenFunction &IGF, Explosion &src,
            Explosion &dest, Atomicity atomicity) const override

void consume(IRGenFunction &IGF, Explosion &src,
               Atomicity atomicity) const override

void fixLifetime(IRGenFunction &IGF, Explosion &src) const override

void packIntoEnumPayload(IRGenFunction &IGF,
                        EnumPayload &payload,
                        Explosion &src,
                        unsigned startOffset) const override

void unpackFromEnumPayload(IRGenFunction &IGF, const EnumPayload &payload,
                          Explosion &dest, unsigned startOffset)
                          const override

```

## RecordTypeBuilder

An abstract calss which gets its implementation class with CRTP technique.

### Functionalities

- `TypeInfo *layout(ArrayRef<ASTField> astFields)`

Resolves `TypeInfo`s of all AST fields

Ask `BuilderImpl::performLayout` to lay the record out with `TypeInfo`s of
all AST fields.

Complete fields (`StructFieldInfo`) with the generated `StructLayout`

Create `TypeInfo`

- Loadable
- Trivial
- Indirect
