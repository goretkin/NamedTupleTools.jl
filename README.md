# NamedTupleTools.jl
### Some NamedTuple utilities


#### Copyright © 2015-2019 by Jeffrey Sarnoff. This work is released under The MIT License.

-----

[![Build Status](https://travis-ci.org/JeffreySarnoff/NamedTupleTools.jl.svg?branch=master)](https://travis-ci.org/JeffreySarnoff/NamedTupleTools.jl)[![codecov](https://codecov.io/gh/JeffreySarnoff/NamedTupleTools.jl/branch/master/graph/badge.svg)](https://codecov.io/gh/JeffreySarnoff/NamedTupleTools.jl)
-----

## Overview

`NamedTuples` are built from fieldnames, given as `Symbols` and field values, as they may be given.
These utilities make some uses of `NamedTuples` a little more straightforward.

## Functional Features

### Construction
- [given `names` and `values`](https://github.com/JeffreySarnoff/NamedTupleTools.jl#construction-from-names-and-values-kristoffer-carlsson)
- [given a `Dict{Symbpl, Any}`](https://github.com/JeffreySarnoff/NamedTupleTools.jl#dict-construction-reconstruction)
- [given a `struct`](https://github.com/JeffreySarnoff/NamedTupleTools.jl#struct-construction-conversion)
- [given a `Vector` of `Pairs`](https://github.com/JeffreySarnoff/NamedTupleTools.jl#vector-of-pairs-peter-deffebach)
- inversive [_Reconstruction_](https://github.com/JeffreySarnoff/NamedTupleTools.jl/blob/master/README.md#reconstruction)

### Reconstruction
- [obtaining `names` and `values`](https://github.com/JeffreySarnoff/NamedTupleTools.jl#construction-from-names-and-values-kristoffer-carlsson)
- [obtaining a `Dict{Symbol, Any}`](https://github.com/JeffreySarnoff/NamedTupleTools.jl#dict-construction-reconstruction)
- [obtaining a `struct`](https://github.com/JeffreySarnoff/NamedTupleTools.jl#struct-construction-conversion)
- [obtaining a `Vector{Pair}`](https://github.com/JeffreySarnoff/NamedTupleTools.jl/blob/master/README.md#convert-to-vector-of-pairs)
- inversive [_Construction_](https://github.com/JeffreySarnoff/NamedTupleTools.jl/blob/master/README.md#construction)

### Selection
- [select one or more named constituents](https://github.com/JeffreySarnoff/NamedTupleTools.jl#select-chad-scherrer)
- complements [_Deletion_](https://github.com/JeffreySarnoff/NamedTupleTools.jl/blob/master/README.md#deletion)

### Deletion
- [delete one or more named constituents](https://github.com/JeffreySarnoff/NamedTupleTools.jl#delete)
- complements [_Selection_](https://github.com/JeffreySarnoff/NamedTupleTools.jl/blob/master/README.md#construction)

### Merging
- [merge one or more NamedTuples](https://github.com/JeffreySarnoff/NamedTupleTools.jl#merge)
- undoes [_Splitting_](https://github.com/JeffreySarnoff/NamedTupleTools.jl/blob/master/README.md#splitting)

### Splitting
- [split a NamedTuple into one or more NamedTuples formed of its consituents](https://github.com/JeffreySarnoff/NamedTupleTools.jl#split)
- undoes [_Merging_](https://github.com/JeffreySarnoff/NamedTupleTools.jl/blob/master/README.md#merging)



## Construction from names and values (Kristoffer Carlsson)
```julia
julia> using NamedTupleTools
julia> namesofvalues  = (:instrument, :madeby)
julia> matchingvalues = ("violin", "Stradivarius")

julia> nt = namedtuple(namesofvalues, matchingvalues)
(instrument = "violin", madeby = "Stradivarius")
```
- The names may be given as `Symbols` or `Strings`
- The names, values may be `Tuples` or `Vectors`

## Selecting Aspects of Elements
```julia
julia> using NamedTupleTools

julia> nt = NamedTuple{(:a, :b)}(1.0, "two")
(a = 1.0, b = "two")

julia> typeof(nt) == NamedTuple{(:a, :b),Tuple{Float64,String}}
true

julia> propertynames(nt) == (:a, :b)
true

julia> fieldnames(nt) == (:a, :b)             # synonym for the moment
true

julia> fieldtypes(nt) == (Float64, String)
true

julia> valtype(nt) == Tuple{Float64, String}
true

julia> fieldvalues(nt) == (1.0, "two")
true
```

## Use NamedTuple prototypes
```julia
using NamedTupleTools

julia> namedtuple(:a, :b, :c)(1, 2.0, "three")
(a = 1, b = 2.0, c = "three")

#=
    namedtuple(  name1, name2, ..  )
    namedtuple( (name1, name2, ..) )
       where the `names` are all `Symbols` or all `Strings`

Generate a NamedTuple prototype by specifying or obtaining the fieldnames.
The prototype is applied to fieldvalues, giving a completed NamedTuple.
=#

julia> ntproto = namedtuple( :a, :b, :c )
NamedTuple{(:a, :b, :c),T} where T<:Tuple

julia> ntproto(1, 2, 3)
(a = 1, b = 2, c = 3)

julia> ntproto("A", "b", 3)
(a = "A", b = "b", c = 3)

julia> isprototype(ntproto)
true

julia> isprototype((a = 1, b = 2, c = 3))
false
```
## Select (Chad Scherrer)
```julia
using NamedTupleTools

julia> nt = (a = 1, b = 2, y = 25, z = 26)
(a = 1, b = 2, y = 25, z = 26)

julia> ay = select(nt, (:a, :y))
(a = 1, y = 25)
```

## Delete
```julia
using NamedTupleTools

julia> ntproto = namedtuple( :a, :b, :c );
NamedTuple{(:a, :b, :c),T} where T<:Tuple

julia> delete(ntproto, :b) === namedtuple(:a, :c)
true

julia> fieldnames(delete(ntproto, :b))
NamedTuple{(:a, :c),T} where T<:Tuple

julia> fieldnames(delete(ntproto, (:a, :c)), fieldnames(delete(ntproto, :a, :c)
(:b,), (:b,)

julia> nt = ntproto(1, 2, 3)
(a = 1, b = 2, c = 3)

julia> delete(nt, :a)
(b = 2, c = 3)

julia> delete(nt, :a, :c)
(b = 2,)
```

## Merge

```julia
# merge from 2..7 NamedTuples

julia> ntproto1 = namedtuple(:a, :b);
julia> ntproto2 = namedtuple(:b, :c);

julia> merge(ntproto1, ntproto2)
NamedTuple{(:a, :b, :c),T} where T<:Tuple
```

```julia
julia> nt1 = (a = 3, b = 5);
julia> nt2 = (c = 8,);

julia> merge(nt1, nt2)
(a = 3, b = 5, c = 8)

julia> nt1 = (a = 3, b = 5);
julia> nt2 = (b = 6, c = 8);

julia> merge(nt1, nt2)
(a = 3, b = 6, c = 8)
```

## Split (Seth Axen)
```julia
julia> using NamedTupleTools

julia> nt = (a = 1, b = 2, c = 3, d = 4);

julia> split(nt, :a)
((a = 1,), (b = 2, c = 3, d = 4))

julia> split(nt, (:a, :b))
((a = 1, b = 2), (c = 3, d = 4))

julia> merge(split(nt, (:a, :b))...) == nt
true
```

## Struct construction, conversion
```
using NamedTupleTools

julia> struct MyStruct
           tally::Int
           team::String
       end

julia> mystruct = MyStruct(5, "hometeam")
MyStruct(5, "hometeam")

julia> mynamedtuple = ntfromstruct(mystruct)
(tally = 5, team = "hometeam")

julia> ntstruct = structfromnt(MyStruct, mynamedtuple)
MyStruct(5, "hometeam")

julia> mystruct == ntstruct
true
```

## Dict construction, reconstruction 
```julia
julia> adict = Dict(:a => 1, :b => "two")
Dict{String,Int} with 3 entries:
  :a => 1
  :b => 2

julia> nt = namedtuple(adict)
(a = 1, b = "two")

julia> adict = Dict(:a => 1, :b => 2//11, :c => "three")
Dict{Symbol,Any} with 3 entries:
  :a => 1
  :b => 2//11
  :c => "three"

julia> nt = namedtuple(adict)
(a = 1, b = 2//11, c = "three")

julia> convert(Dict, nt) == adict
true
```

## Vector of Pairs (Peter Deffebach)
```julia
julia> vec = [:a => 1, :b => 2]
2-element Array{Pair{Symbol,Int64},1}:
 :a => 1
 :b => 2

julia> nt = namedtuple(vec)
(a = 1, b = 2)
```

## convert to Vector Of Pairs
```julia
julia> nt = (a=1, b=2);
julia> convert(Vector{Pair}, nt)
2-element Array{Pair{Symbol,Int64},1}:
 :a => 1
 :b => 2
 
nt = (a = 1, b = "two", c = 3.0);
vec = convert(Vector{Pair}, nt)
3-element Array{Pair{Symbol,B} where B,1}:
 :a => 1
 :b => "two"
 :c => 3.0
```

## Variables mixed with standard syntax (Sebastian Pfitzner, Takafumi Arakaki)
```julia
julia> a, b, c, d, f = 1, 1.0, 1//1, "one", (g=1,)
(1, 1.0, 1//1, "one", (g = 1,))

julia> nt = @namedtuple(a, b, c, d, e = a + b, f...)
(a = 1, b = 1.0, c = 1//1, d = "one", e = 2.0, g = 1)
```
