# Reading Explosion.h

## Explosion

A list of raw LLVM values.

The meaning of these values is imposed externally by the type infos, except
that it is expected that they will be passed as arguments in exactly this
way.

### Members

* `unsigned NextValue`
  * Indicates the lastest index of claimed LLVM values.
  * Floors the lower bound of the collection that `Explosion` represents.
  * Monotonically increased as long as "claiming" operations happen.

* `SmallVector<llvm::Value*, 8> Values`
  Stores LLVM values.

### Operations Summary

* Basic CRUD and collection operation.
  The lower bound of the collection is floored by ![`NextValue`](#Members).

* Transferring ownership of values from another `Explosion` to `this`.

* Claiming a value in the `Explosion`:
  Increases `NextValue` by the amount of values that to be claimed.

* Taking a value from the `Explosion`:
  A composition of claiming and removing. Thus this operation does not
  increase `NextValue`.

* Resetting the `Explosion`:
  Sets `NextValue` to zero and clears `Values`.

### Operations / Creation

* Default constructor
* Copy constructor = `deleted`
* Copy assignment operator = `deleted`
* Move constructor
* Move assignment operator

### Operations / Deletion

* Destructor

### Operations / Retrieve

* Collection operations (`empty`, `size`, `begin`, `end`)
* `getRange`
* `getAll`

### Operations / Update

* `add`, singular
* `add`, plural
* `insert`

### Operations / Specialized

* `transferInto`

* `markClaimed`

* `claimNext`

* `claim`

* `claimAll`

* `getLastClaimed`

* `takeLast`

* `reset`

* `print`

* `dump`

## ExplosionSchema::Element

The schema for one atom of the explosion.

### Members

* `llvm::Type *Type`

* `Alignment::int_type Align`

### Operations / Creation

* For scarlar

```cpp
static Element forScalar(llvm::Type *type)
```

* For aggregate

```cpp
static Element forAggregate(llvm::Type *type, Alignment align)
```

### Operations / Retrieve

* `isScalar`

* `getScalarType`

* `isAggregate`

* `getAggregateType`

* `getAggregateAlignment`

## ExplosionSchema

Essentially the type of an Explosion.

### Members

* `SmallVector<Element, 8> Elements`
  Stored elements.

* `bool ContainsAggregate`
  An **or'ed** union of each element's `isAggregate()` return value.

### Operations

* Collection operations (size, empty, begin, end)

* `add`: Adding element

* `containsAggregate`: Tests if it contains aggregate

* `isSingleAggregate`: Tests if it contains only one aggregate

* `getScalarResultType`: Produce the correct type for a **direct return** of
  this schema, which is **assumed to contain only scalars**.  This is
  defined as:
  * `void`, if the schema is empty;
  * the element type, if the schema contains exactly one element;
  * an anonymous struct type concatenating those types, otherwise.
