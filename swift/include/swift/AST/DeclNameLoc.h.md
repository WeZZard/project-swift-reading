# Reading DeclNameLoc.h

## class DeclNameLoc

### Members

- `LocationInfo : const void *`:

- `NumArgumentLabels : unsigned`:

### Polymorphism

`LocationInfo` is typed with `const void *` which means it can be any 
thing. Actually it depends on value of `NumArgumentLabels`.

If `NumArgumentLabels` = 0, `LocationInfo` is the `SourceLoc` for the base
name.

Otherwise, `LocationInfo` points to an array of `SourceLoc`s, which
contains:
* The base name location
* The left parentheses location
* The right parentheses location
* The locations of each of the argument labels.
