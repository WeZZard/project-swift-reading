# Reading IRGenSIL.cpp

## DynamicallyEnforcedAddress

## CoroutineState

## LoweredValue

Represents a SIL value lowered to IR. A summed type of 

```cpp
class ContainedAddress
```

An address in memory together with the local allocation which owns it.

```cpp
class StackAddress
```

An address on the stack together with an optional stack pointer reset location.

```cpp
struct DynamicallyEnforcedAddress
```

The lowered value of a `begin_access` instruction using dynamic enforcement.

```cpp
using ExplosionVector = SmallVector<llvm::Value *, 4>;
```

A normal value, represented as an exploded array of llvm Values

```cpp
using SingletonExplosion = llvm::Value*;
```

The special case of a single explosion.

```cpp
class FunctionPointer
```

A function pointer value.

```cpp
class ObjCMethod
```

Represents an ObjC method reference that will be invoked by a form of `objc_msgSend`

```cpp
struct CoroutineState
```

A coroutine state.

```cpp
void
```

A special case of an empty explosion.

### Special Usages

- Produce an explosion for this lowered value.
`Explosion getExplosion(IRGenFunction &IGF, SILType type) const`

- Produce an explosion which is known to be a single value.
`llvm::Value *getSingletonExplosion(IRGenFunction &IGF, SILType type)`

- Produce a callee from this value.
`Callee getCallee(IRGenFunction &IGF, llvm::Value *selfValue, CalleeInfo &&calleeInfo) const;`

## LoweredBB

> Represents a lowered SIL basic block. This keeps track of SIL branch arguments so that they can be lowered to LLVM phi nodes.

```cpp
using PHINodeVector = llvm::TinyPtrVector<llvm::PHINode*>;

struct LoweredBB {
  llvm::BasicBlock *bb;
  PHINodeVector phis;
  
  LoweredBB() = default;
  explicit LoweredBB(llvm::BasicBlock *bb, PHINodeVector &&phis)
    : bb(bb), phis(std::move(phis))
  {}
};
```

Just simply encapsulated an `llvm::BasicBlock` and `PHINodeVector`
## IRGenSILFunction

Base classes: 
- `IRGenFunction`
- `SILInstructionVisitor<IRGenSILFunction>`

### Member Variables:

```cpp
[SILValue : LoweredValue] LoweredValues
[SILValue : StackAddress] LoweredPartialApplyAllocations
[SILValue : LoweredValue] LoweredUndefs
Set<SILInstruction *>.    StackAllocs
```

```cpp
[(unsigned, (SILDebugScope *, StringRef)) : Address] ShadowStackSlots
[llvm::Value * : Address] TaskAllocStackSlots
[Decl * : SmallString<4>] AnonymousVariables
Set<llvm::Value *> ValueVariables
[(llvm::Value *, DominancePoint)] ValueDomPoints
unsigned NumAnonVars = 0
```

```cpp
int EstimatedStackSize = -1
```

```cpp
[SILBasicBlock * : LoweredBB]~Ordered LoweredBBs
```

```cpp
[llvm::BasicBlock] FailBBs
```

```cpp
SILFunction * CurSILFn
```

```cpp
Address IndirectReturn
```

```cpp
DominanceInfo * Dominance
```

### Member Functions:

```cpp
void emitSILFunction

void estimateStackSzie

bool isAddress

void setLoweredValue

void setLoweredAddress

void setLoweredStackAddress

void setLoweredDynamicallyEnforcedAddress

void setContainerOfUnallocatedAddress

void overwriteAllocatedAddress

void setAllocatedAddressForBuffer

void setLoweredExplosion

void setCorrespondingLoweredValues

void setLoweredBox

void setLoweredFunctionPointer

void setLoweredObjCMethod

void setLoweredObjCMethodBounded

void setLoweredCoroutine

LoweredValue& getUndefLoweredValue

LoweredValue& getLoweredValue

Address getLoweredAddress

StackAddress getLoweredStackAddress

llvm::Value * getLoweredDynamicEnforcementScratchBuffer

const CoroutineState& getLoweredCoroutine

void getLoweredExplosion

Explosion getLoweredExplosion

llvm::Value * getLowredSingletonExplosion

LowreedBB& getLoweredBB

TypeExpansionContext getExpansionContext

SILType getLoweredTypeInContext

StringRef getOrCreateAnonymousVarName

StringRef getVarName

void zeroInit

bool emitLifetimeExtendingUse

void emitDebugVariableRangeExtension

bool needsShadowCopy

bool canAllocaStoreValue

static bool isCallToSwiftTaskAlloc

static bool isTaskAlloc

llvm::Value * emitTaskAllocShadowCopy

llvm::Vlaue * emitShadowCopy
 bool shouldShadowVariable

bool shouldShadowStorage

llvm::Vlaue * emitShadowCopyIfNeeded

llvm::Vlaue * emitShadowCopyIfNeeded (overload)

void emitShadowCopyIfNeeded

void bindArchetypes

void emitDebugVariableDeclaration

void emitFailBB
```

### SIL Instruction Lowering

Lowers SIL instructions to LLVM IR values via visitor pattern.

### Helping SIL Functions IR Emission