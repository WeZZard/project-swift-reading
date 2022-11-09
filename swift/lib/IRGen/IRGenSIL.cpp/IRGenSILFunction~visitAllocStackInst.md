# Reading IRGenSILFunction::visitAllocStackInst

## Prototype

```cpp
void IRGenSILFunction::visitAllocStackInst(swift::AllocStackInst *i)
```

## Process

SILType * t <- swift::AllocStackInst::getElementType(i)

TypeInfo& typeInfo <- getTypeInfo(t)

stackAddr <- type.allocateStack(*this, i->getElementType(), dbgname);

setLoweredStackAddress(i, stackAddr)

IF i->getDecl()
	zeroInit()

emitDebugInfoForAllocStack