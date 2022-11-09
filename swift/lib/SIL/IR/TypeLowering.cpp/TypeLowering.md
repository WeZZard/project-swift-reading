# TypeLowering

## Subclasses

- TypeLowering
	- LoadableTypeLowering
		- TrivialTypeLowering
		- NonTrivialLoadableTypeLowering
			- LoadableAggTypeLowering
				- LoadableTupleTypeLowering
				- LoadableStructTypeLowering
				- NormalDifferentiableSILFunctionTypeLowering
				- LinearDifferentiableSILFunctionTypeLowering
			- LoadableEnumTypeLowering
			- LeafLoadableTypeLowering
				- ReferenceTypeLowering
				- Loadable UNOWNED reference storage lowering series
				- OpaqueValueTypeLowering
	- AddressOnlyTypeLowering
		- UnsafeValueBufferTypeLowering

