# Reading StructLayout.h


## Comments

Layout info for aggregated types.

Frankly an aged file.

## Outline

### StructLayout.h

- enum class LayoutStrategy
- enum class LayoutKind
- class NonFixedOffsetImpl
- class ElementLayout
	- enum class Kind
- class StructLayoutBuilder
- void applyLayoutAttributes(IRGenModule&, NominalTypeDecl *, bool, Alignment&)
- class StructLayout
- getDefaultActorStorageFieldOffset

## ElementLayout

### Conclusion
Representing the layout for a single element of some sort of aggregate structure by composing up `TypeInfo *`,  offset in bytes from the start of the struct (`unsigned` x 2), index of this element (`unsigned : 28`), POD indicator and kind info (`Kind`).

## StructLayout

### StructLayout::StructLayout:

BUILDS `Elements : [ElementLayout]` WITH EACH IN `types: [const TypeInfo *]` by `Element::getIncomplete(*type)`

`build` <-` StructLayoutBuilder(IGM)`

OPTIONAL: `builder.addHeapHeader()`

`builder.addFields(Elements, strategy)`

SETTING UP:
- `MinimumAlign`
- `MinimumSize`
- `headerSize`
- `IsFixedLayout`
- `IsKnownPOD`
- `IsKnownBitwiseTakable`
- `IsKnownAlwaysFixedSize`
- `Ty`

IF HAS `NominalTypeDecl * decl`: EXECUTE `applyLayoutAttributes`

## StructLayoutBuilder

Stricly speaking, this is a **“structure layout builder”** which is designed for both `struct` and a base class for `class`’s `ClassLayoutBuilder`.

### Constructor

StructLayoutBuilder(IRGenModule& IGM)

### Adding Headers

void addHeapHeader();

void addNSObjectHeader();

void addDefaultActorHeader(ElementLayout& elt);

### Adding Fields

bool addFields(llvm::MutableArrayRef<ElementLayout> fields, LayoutStrategy strategy);

bool addField(ElementLayout& elt, LayoutStrategy strategy);

### Accessing Struct Layout Info

bool empty() const;

[llvm::Type *] getStructFields() const;

bool isFixedLayout() const;

IsPOD_t isPOD() const;

IsBitwiseTakable_t isBitwiseTakable() const;

IsFixedSize_t isAlwaysFixedSize() const;

Size getSize() const;

Size getHeaderSize() const;

Alignment getAlignment() const;

SpareBitVector getSpareBits() const;

### Build the Current Elements as A New Anonymous Struct Type
llvm::StructType * getAsAnonStruct() const;

void setAsBodyOfStruct(llvm::Struct * type) const;