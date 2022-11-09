# Reading IRGenFunction.h

The file defines the structure used to generate the IR body of function.

## IRGenFunction

Emitting LLVM instructions for a specific function (or say, calls to “primitive” SIL instructions)

### Decl Head

```cpp
class IRGenFunction {
public:
	IRGenModule &IGM;
	IRBuilder Builder;

	OptimizationMode OptMode;

	llvm::Function *CurFn
```

### Function prologue and epilogue

```cpp
private:

	Address ReturnSlot;
	llvm::BasicBlock *ReturnBB;
	llvm::Value *CalleeErrorResultSlot = nullptr;
	llvm::Value *AsyncCalleeErrorResultSlot = nullptr;
	llvm::Value *CallerErrorResultSlot = nullptr;
	llvm::Value *CoroutineHandle = nullptr;
	llvm::Value *AsyncCoroutineCurrentResume = nullptr;
	llvm::Value *AsyncCoroutineCurrentContinuationContext = nullptr;

	Address asyncContextLocation;

	llvm::BasicBlock *CoroutineExitBlock = nullptr;
```

### Helper methods

#### Reference-counting methods

```cpp
private:

	llvm::Instruction *AllocaIP;
	const SILDebugScope *DbgScope;
	llvm::Instruction *EarliestIP;
```

#### Expression emission

### Declaration emission

### Type emission

```cpp
private:
	LocalTypeDataCache &getOrCreateLocalTypeData();

	LocalTypeDataCache *LocalTypeData = nullptr;

	DominanceResolverFunction DominanceResolver = nullptr;
	DominancePoint ActiveDominancePoint = ...;
	ConditionalDominanceScope *ConditionalDominance = nullptr;

	llvm::Value *SelfValue = nullptr;
	CanType SelfType;
	bool SelfTypeIsExact = false;
	DynamicSelfKind SelfKind;
```
