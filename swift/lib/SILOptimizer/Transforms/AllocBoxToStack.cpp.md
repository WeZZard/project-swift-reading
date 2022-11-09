# Reading AllocBoxToStack.cpp
## Outline

1. Statistics
	1. NumStackPromoted
2. Options
	1. -max-local-apply-recur-depth
	2. -allocbox-to-stack-analyze-apply
3. SIL Utilities for alloc_box Promotion
	1. stripOffCopyValue
	2. useCaptured
4. Liveness for alloc_box Promotion
	1. successorHasLiveIn
	2. propagateLiveness
	3. addLastRelease
	4. getFinalReleases
5. alloc_box Escape Analysis
	1. partialApplyEscapes
	2. applyArgumentEscapes
	3. recursivelyFindBoxOperandsPromotableToAddress
	4. checkLocalApplyBody
	5. isOptimizableApplySite
	6. diagnose
	7. canPromoteAllocBox 
6. alloc_box Promotion
	1. replaceProjectBoxUsers
	2. rewriteAllocBoxAsAllocStack
	3. class PromotedParamCloner
	4. specializeApplySite
	5. rewriteApplySites
	6. rewritePromotedBoxes
	7. class AllocBoxToStack
	8. swift::createAllocBoxToStack

