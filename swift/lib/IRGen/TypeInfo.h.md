# Reading lib/IRGen/TypeInfo.h

## Outline

- enum class FixedPacking
- enum class SpecialTypeInfoKind
- class TypeInfo

## TypeInfo

Base class defines interface used to perform primitive operations on swift values and objects.

### Members

- A union of size of `uint64_t`

  - Either `TypeInfo`
  - Or `FixedTypeInfo`

- `mutable const TypeInfo *NextConverted`

- `llvm::Type *StorageType;`

- `mutable NativeConventionSchema *nativeReturnSchema = nullptr;`

- `mutable NativeConventionSchema *nativeParameterSchema = nullptr;`

### Subclasses

- `TypeInfo`
  - `FixedTypeInfo`: provides a number of operations meaningful only for types with a fixed-size representation
    - `LegacyTypeInfo`
    - `LoadableTypeInfo`
      - `ExecutorTypeInfo` via `TrivialScalarPairTypeInfo`
      - `IntegerLiteralTypeInfo` via `TrivialScalarPairTypeInfo`
      - `ReferenceTypeInfo`: a further refinement of `FixedTypeInfo` which provides operations meaningful only for types with reference semantics
        - `ClassTypeInfo` which inherited from `HeapTypeInfo` which inherited from `ReferenceTypeInfo` 
        - `FuncTypeInfo` via `ScalarPairTypeInfo`
      - `OpaqueStorageTypeInfo` via `ScalarTypeInfo`
    - `BlockStorageTypeInfo` via `IndirectTypeInfo`
    - `Nativeness` ##Name## `ReferenceTypeInfo` via `IndirectTypeInfo`
    - `ImmovableTypeInfo` via `IndirectTypeInfo`

### Important Subclasses

- FixedTypeInfo

- LoadableTypeInfo

### CRTP Dispatcher Types

- `ScalarTypeInfo`
  - `ScalarPairTypeInfo`
    - `TrivialScalarPairTypeInfo`
  - `SingleScalarTypeInfo`
    - `SingleScalarTypeInfoWithTypeLayout`
      - `HeapTypeInfo` which inherited from `ReferenceTypeInfo`
- `IndirectTypeInfo`
  - `WitnessSizedTypeInfo` which inherited from `TypeInfo`
    - `ResillienceTypeInfo`

### Functionalities

- Unsafe downcast
- Getting type info
  - is known empty
  - is ABI accessible
  - is POD
  - is bitwise takable
  - special type info kind
  - subclass kind
  - is fixed sized
  - is fixed size with given resilience expansion
  - is loadable
  - get known alignment
- Emitting
  - storage type
  - address for pointer
  - undef address
  - value witness
    - *virtual* size
    - *virtual* alignment mask
    - *virtual* stride
    - *virtual* is pod
    - *virtual* is bitwise takable
    - *virtual* dynamically packed inline
  - *virtual* static-size
  - *virtual* static-alignment-mask
  - *virtual* static-stride
- *virtual* explosion schema
- *virtual* build type layout entry (`TypeLayoutEntry`) with `SILType`
- Emitting LLVM-IR for
  - *virtual* allocate stack in `IRGenFunction` with `SILType` and `llvm::Twine` //?
  - *virtual* deallocate stack in `IRGenFunction` with `StackAddress` and `SILType`
  - value witness
    - assign with copy
    - assign with take
    - initialize with take
    - initialize with coy
    - initialize buffer with copy of buffer
    - initialize from parameters
    - destroy
  - *virtual* is single retainable pointer
  - is single Swift retainable pointer
  - value witness
    - *virtual* may have extra inhabitants
  - *virtual* can value witness extra inhabitants ups to index
  - value witness
    - get enum single payload
    - store enum single payload
    - store extra inhabitant tag dynamic
  - fixed packing
  - index array of objects in this type
  - round up to type alignment
  - *virtual* destroy array
  - *virtual* initialize array with copy
  - *virtual* initialize array with take (no alias)
  - *virtual* initialize array with take (front-to-back)
  - *virtual* initialize array with take (back-to-front)
  - *virtual* assign array with copy
  - *virtual* assign array with copy (no alias)
  - *virtual* assign array with copy (front-to-back)
  - *virtual* assign array with copy (back-to-front)
  - *virtual* assign array with take
- *virtual* collect metadata for outlining
- get native return value schema
- get parameter value schema
- Emit verifier code that compares compile-time constant knowledge of this kind of type's traits to its runtime manifestation.
- Emit call outlined copy
- Emit call outlined destroy
