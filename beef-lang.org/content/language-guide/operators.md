+++
title = "Operators"
weight=75
+++

## Operators overview
These following operator groups are listed from the highest priority group to the lowest priority group.

### Primary operators
* `x.y` - member access
* `x..y(<method args>)` - member access cascade. Overrides result of method `y` with `x`, which is useful for chaining method calls to a common target. For example: ``string..Append("name: ").Append(name);`` Both ``Append`` calls are targeted on ``string`` even though ``Append`` returns ``void``.
* `x?.y` - null conditional member access. Results in null if `x` is null.
* `f(x)` - method call
* `a[x]` - array indexing

### Unary operators {#unary}
* `x++` - postfix increment, increments x and results in result before the increment
* `x--` - postfix decrement, decrements x and results in result before the decrement
* `++x` - prefix increment, increments x and results in the new value
* `--x` - prefix decrement, decrements x and results in the new value
* `~x` - bitwise complement
* `!x` - logical negation
* `(T)x` - type casts `x` to type `T`
* `&x` - address of `x`
* `*x` - dereference pointer `x`
* `x(..y, <other method args>)` - argument cascade. Overrides result of method `x` with `y`, which is useful for reusing an argument. For example, see [string ease of use]({{< ref "strings.md#ease" >}}).

#### Multiplicative operators {#binary}
* `x * y` - multiplication
* `x &* y` - multiplication with overflow checking disabled. 
* `x / y` - division. If `x` and `y` are integers, results in an integer truncated toward zero.
* `x % y` - remainder. If `x` and `y` are integers, results in the remainder of the division `x / y`.

#### Additive operators
* `x + y` - addition
* `x &+ y` - addition with overflow checking disabled
* `x - y` - subtraction
* `x &- y` - subtraction with overflow checking disabled. Allows for subtracing two unsigned integers without resulting in a signed integer.

### Shift operators
* `x << y` - shift bits left
* `x >> y` - shift bits right. if `x` is signed, the left bits are filled with the sign bit.

### Spaceship operator
* `x <=> y` - results is negative if `x < y`, zero if `x == y`, positive if `x > y`

### Comparison operators
* `x < y`
* `x > y`
* `x <= y`
* `x >= y`
* `x is T` - results in true if `x` can be cast to type `T`
* `x as T` - casts `x` to `T` if the cast is possible, otherwise results in null

### Logical AND operator
* `x & y` - bitwise AND

### Logical XOR operator
* `x ^ y` - bitwise XOR

### Logical OR operator
* `x | y` - bitwise OR   

### Equality operator

* `x == y`
* `x === y` - strict equality
* `x != y`
* `x !== y` - strict inequality

The strict equality operators can be used to check reference equality, skipping any equality operator overloads. For value types such as structs or tuples, the strict equality operator will perform a memberwise strict equality check.

### Conditional AND operator
* `x && y`

### Conditional OR operator
* `x || y`

### Null-coalescing operator
* `x ?? y` - results in `x` if it is non-null, otherwise results in `y`

### Conditional operator {#conditional}
* `x ? y : z` - results in `y` is `x` is true, otherwise results in `z`

### Assignment operators {#assignment}

Assignments result in the new value of `x`.

* `x = y`
* `x += y`
* `x -= y`
* `x *= y`
* `x /= y`
* `x %= y`
* `x |= y`
* `x ^= y`
* `x <<= y`
* `x >>= y`
* `x ??= y`
* `=> x` - Method binding operator

#### Type attribute operators {#typeattr}
* `sizeof(T)` - size of `T`. Note that reference types will always result in the native pointer size  
* `alignof(T)` - alignment of `T`
* `strideof(T)` - size of `T`, aligned to the alignment of `T`
* `alloctype(T)` - result of `new T()`, which will be `T` for reference types and `T*` for valuetypes
* `comptype(x)` - type of reflected type `x`
* `decltype(x)` - type of result of expression `x`. Any expression is allowed, including method calls, but will only be evaluated to determine the type of `x` and won't generate any executable code.
* `nullable(T)` - `T` if `T` is already nullable, otherwise `T?`
* `rettype(T)` - return type of a delegate or function
* `typeof(T)` - reflected type of type `T`
* `offsetof(T, Tfield)` - where `Tfield` is a field of `T`, returns the offset of `Tfield` on `T`

### Ref operators
* `ref x` - required for passing values into ref parameters or other values expecting `ref`
* `out x` - required for passing values into out parameters
* `var x` - create a new variable `x` from an out parameter
* `let x` - create a new const/readonly variable `x` from an out parameter

### Params operator
* params x - where x is a variadic parameter, will pass through those params to another variadic parameter. Where x is a delegate or function params, will expand those in place.

See [Variable argument counts]({{< ref "datatypes/members.md#params" >}}) for examples.

### Range operators {#range}
* `x...y` - creates an inclusive range from x up to including y
* `x..<y` - creates an exclusive range from x up to but excluding y

* `x...` - creates an index range from x up to including the end of the collection
* `...y` - creates an index range from the start of the collection up to including y
* `x...^y` - creates an index range from x up to including y, but counted from the back (See Index operator below). For example from the `x`th element of the list to the `y`th, but counted from the back, element.

See [Range expression]({{< ref "expressions.md#range" >}}) for examples.

### Index from end operator
* `^x` - creates an Index for the `x`th element counting from the back (`.FromEnd`), where the last element is ^1 (^0 is equal to the count/length of the collection)

`Index` is mostly used for indexing collections and constructing ranges.

```C#
let list = scope List<int>() { 5, 1, 0 };

let first = list[0];
// first == 5

// Indexing from the back starts at Count (3 in this case), which is out of range. Thus we get the last element by counting down one -> ^1
let last = list[^1];
// last == 0
```

## Casting

The `(T)x` cast operator can directly perform many kinds of type conversions, but there are some special cases:
* Unboxing. `(T)obj` where `obj` is an `Object` and `T` is a valuetype will perform an boxing. This unboxing can fail at runtime in Debug mode (when Dynamic Cast Checks are enabled). You can use an `obj is T` check or a `obj as T?` expression to safely unbox.
* Retrieving an object's address: the expression `(void*)obj` where `obj` is an Object type is actually an unboxing request, not a type conversion. `System.Internal.UnsafeCastToPtr` can return the address of an Object as a `void*`.
* Casting to an unrelated type. Sometimes double-casts can be used to achieve what would otherwise be an illegal cast. For example, with `(void*)handle` where `handle` is a the typed primitive `struct Handle : int`, the cast directly to `void*` is not allowed, but `(void*)(int)handle` is allowed.

## Operator overloading

Structs and classes can provide operator overloads. Comparison operator selection is flexible, in that not all combination of <, <=, ==, !=, >, and >= need to be defined. The "inverse" of operators will be called if available, or if just the <=> operator is defined then that can be used for all comparison types as well.

```C#
struct Vector2
{
	float x;
	float y;

	public this(float x, float y)
	{
		this.x = x;
		this.y = y;
	}

	/* Binary + operator */
	public static Vector2 operator+(Vector2 lhs, Vector2 rhs)
	{
		return .(lhs.x + rhs.x, lhs.y + rhs.y);
	}

	/* Unary '-' operator */
	public static Vector2 operator-(Vector2 val)
	{
		return .(-val.x, -val.y);
	}

	/* Unary '++' operator */
	public static Vector2 operator++(Vector2 val)
	{
		return .(val.x + 1, val.y + 1);
	}

	/* Non-static unary '--' operator */
	public void operator--() mut
	{
		x--;
		y--;
	}

	/* Assignment '+=' operator */
	public void operator+=(Vector2 rhs) mut
	{
		x += rhs.x;
		y += rhs.y;
	}

	/* Comparison operator */
	public static int operator<=>(Vector2 lhs, Vector2 rhs)
	{
		/* Compare on X, or on Y if X's are equal */
		int cmp = lhs.x <=> rhs.x;
		if (cmp != 0)
			return cmp;
		return lhs.y <=> rhs.y;
	}

	/* Conversion operator from float[2] */
	public static operator Vector2(float[2] val)
	{
		return .(val[0], val[1]);
	}
}
```

Binary operators can be marked with the `[Commutable]` attribute, which allows for certain operator transformations. A commutable "A < B" operator, for example, can be used for "B > A", "!(A >= B)", and !(B <= A). A commutable "A == B" operator can be used for "B == A", "!(A != B)", and "!(B != A)".
