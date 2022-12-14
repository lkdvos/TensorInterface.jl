# TensorInterface

A Julia interface proposal for tensor-like objects.

### Context
In analogy to the interface provided by VectorInterface.jl, this package proposes a small set of methods that should be supported by types representing objects that can be interpreted as living in a tensor product of vector spaces. These methods can then be used to build complex algorithms in a universal and agnostic manner. 

In particular, we will consider tensors as a set of objects with the basic properties that:

* Tensors are vectors (in the sense that they support the interface provided by VectorInterface.jl)
* Tensors have the additional internal structure of an ordered tensor product of vector spaces, which can be permuted. (in more general cases, permuted can be replaced with braided, or this operation might only make sense for cyclic permutations, ...)
* Tensors can be pairwise contracted over a subset of their indices, using the inner product of the underlying vector spaces. In particular this supports the idea of using a bipartition of the indices to interpret the tensor as a map from one part of the vector spaces to the other, with contraction being equivalent to composition of maps.
* Tensors can be (partially) traced, which can be interpreted as a contraction of a tensor with an identity map.

### Interface
Thus, generically we have tensors with ``N₁`` ``(N₂)`` outgoing (incoming) vector spaces, which support the following methods:

*   `numin(t)` and `numout(t)` give the amount of incoming and outgoing vector spaces.
*   `zerotensor(t, T, p₁::NTuple{Int}, p₂::NTuple{Int}=(,))` creates a zero tensor of similar type, but with a modified scalar type, and a permuted set of underlying vector spaces. 
*   `zerotensor(t₁, id₁, t₂, id₂, T, p₁::NTuple{Int}, p₂::NTuple{Int}=(,))` creates a zero tensor of similar type, but with a modified scalar type, and a permuted set of underlying vector spaces selected from two different tensors. (useful in scenarios involving pairwise operations)
*   `tensorpermute(t₁, p₁, p₂=(,))` permutes the underlying vector spaces.
*   `tensoradd(t₁, labels₁, t₂, labels₂, α, β)` computes `β*t₁ + α*tensorpermute(t₂, p1, p2)`. The permutation is determined by identifying labels. Additionally `tensoradd!` and `tensoradd!!` store the result in `t₁`.
*   `tensorcontract(t₁, labels₁, t₂, labels₂, C₂, t₃, labels₃, C₃, α, β)` computes `β*t₁ + α * t₂ * t₃` where the labels are used to identify the underlying spaces, and `Cᵢ` indicates whether conjugated tensors should be used. `tensorcontract!` and `tensorcontract!!` store the result in `t₁`.
*   `tensortrace(t₁, labels₁, t₂, labels₂, α, β)` computes `β*t₁ + α * trace(t₂)` where the trace happens over the identical labels in `labels₂`. `tensortrace!` and `tensortrace!!` store the result in `t₁`.

### Notes
*   The methods using labels could be defined in terms of permutations, which clutters the interface but might be slightly more efficient. The labels do however make the distinction between `Tensors` and `TensorMaps` redundant. Defining both is also an option?
*   `tensor-` as prefix for the methods might not be necessary?
*   `adjoint` and `conj` and `transpose`?
*   order of the arguments?
*   `tensorproduct`?
