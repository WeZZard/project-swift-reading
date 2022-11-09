# Reading GenStruct.h

## Outline 
### irgen

- projectPhysicalStructMemberAddress

uses in `IRGenSILFunction::visitStructElementAddrInst`

>`IRGenSILFunction` is a class that visits a SIL Function and generates LLVM IR.


- projectPhysicalStructMemberFromExplosion

used in `IRGenSILFunction::visitStructExtractInst`

- emitPhysicalStructMemberFixedOffset

used in `StructMetadataBuilderBase::addFieldOffset`

- getPhysicalStructMemberAccessStrategy

used in `RemoteASTContextImpl::getOffsetOfFieldFromIRGen`

- getPhysicalStructFieldTypeInfo

used in `IRGenDebugInfoImpl::handleFragmentDIExpr`

- getPhysicalStructFieldIndex

used in `irgen::emitConstantValue`
