# Introduction to JS 2 | Operators | Conditionals | Loops basic

- Operators
- Operations
	- 1 + 1 → 2
	- "1" + 1 → "11"
	- "1" - 1 → 0
- Javascript is not weird, it's just people have never read the documentation.
	- defined in ECMA Script
- [[Coercion]] → type conversion
	- What is Coercion?
	- It can be manual or automatic (based on certain algorithms)
		- manual → explicit type conversion
		- automatic → implicit type conversion
- [[Abstract Operations]]
	- These are some set of algorithms, that is present in the ecmascript docs, but they are not available for usage in ecmascript i.e. we as developers cannot use these operation directly.
	- They are mentioned in the docs to help the documentation only.
	- In the ecma docs there are a lot of things that are done by the language internally. To explain these internal details of how and what language is doing, we have abstract operations mentioned in the docs.
- Type Conversion
	- The ECMAScript language implicitly performs automatic type conversion as needed. To clarify the semantics of certain constructs it is useful to define a set of conversion abstract operations. The conversion abstract operations are polymorphic; they can accept a value of any ECMAScript language type. But no other specification types are used with these operations.