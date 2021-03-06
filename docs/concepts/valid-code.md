## No Classes Allowed!
Contracting maintains a strict 'no classes' model. This forces you as the developer to create more procedural code that is explicit and completely self-contained. Contracts must be easy to read and understand for validity. Instead of thinking of your code in classes, think of each contract as a 'module' that exposes certain methods to it's users.

All `class` keywords will fail your contract on submission. Even if you try to use classes for object oriented code, you will have to find another way to express your structures.

For example:

```python
class Car:
	def __init__(self, make, model):
		self.make = make
		self.model = model
```

This is illegal and will fail. Instead, think of each smart contracting having access to storage space. To access this storage space, you use a [Hash](../storage/#hash-api) or a [Variable](../storage/#variable-api) object. Hash objects are ['auto-vivifying'](../storage/#multihashes) which just means they are dynamic. You can access and store data into certain keys by using the following pattern:

```python
cars = Hash()

# First key is the primary key, or the name of the object
cars['balthasar', 'make'] = 'Ford'
cars['balthasar', 'model'] = 'Contour'
```

Read more about storage in the [Storage](../storage) section.

## Restricted Builtins

Certain builtins such as `exec`, `eval`, and `compile` are obviously dangerous. We do not want to allow any arbitrary execution of code.

Here is a full list of Python3.6 builtin methods versus the ones we allow in Contracting. NOTE: All exceptions except the base Exception class are removed from Contracting.

Built-Ins	|	Python3.6	|	Contracting	| Reason for Restriction
-	|	-	|	-	|	-	
[abs()](https://docs.python.org/3/library/methods.html#abs)						|	✓	|	✓	|	
[all()](https://docs.python.org/3/library/methods.html#all)						|	✓	|	✓	|	
[any()](https://docs.python.org/3/library/methods.html#any)						|	✓	|	✓	|
[ascii()](https://docs.python.org/3/library/methods.html#ascii)					|	✓	|	✓	|
[bin()](https://docs.python.org/3/library/methods.html#bin)						|	✓	|	✓	|
[bool()](https://docs.python.org/3/library/methods.html#bool)						|	✓	|	✓	|
[bytearray()](https://docs.python.org/3/library/methods.html#func-bytearray)		|	✓	|	✓	|
[bytes()](https://docs.python.org/3/library/methods.html#func-bytes)				|	✓	|	✓	|
[callable()](https://docs.python.org/3/library/methods.html#callable)				|	✓	|	✘	| methods are not passed as objects in Contracting.
[chr()](https://docs.python.org/3/library/methods.html#chr)						|	✓	|	✓	|
[classmethod()](https://docs.python.org/3/library/methods.html#classmethod)		|	✓	|	✘	| Classes are disabled in Contracting.
[compile()](https://docs.python.org/3/library/methods.html#compile)				|	✓	|	✘	| Arbitrary code execution is a high security risk.
[complex()](https://docs.python.org/3/library/methods.html#complex)				|	✓	|	✘	| Complex numbers are potentially non-deterministic. This is a consensus failure risk.
[copyright](https://docs.python.org/3/library/constants.html#copyright)				|	✓	|	✘	| Unnecessary.
[credits](https://docs.python.org/3/library/constants.html#credits)					|	✓	|	✘	| Unnecessary.
[delattr()](https://docs.python.org/3/library/methods.html#delattr)				|	✓	|	✘	| Arbitrary removal of Python attributes could allow unauthorized access to private objects and methods.
[dict()](https://docs.python.org/3/library/methods.html#func-dict)				|	✓	|	✓	|
[dir()](https://docs.python.org/3/library/methods.html#dir)						|	✓	|	✘	| Allows exploration path into security exploit development.
[divmod()](https://docs.python.org/3/library/methods.html#divmod)					|	✓	|	✓	|
[enumerate()](https://docs.python.org/3/library/methods.html#enumerate)			|	✓	|	✘	| Potentially safe. Evaluating to make sure.
[eval()](https://docs.python.org/3/library/methods.html#eval)						|	✓	|	✘	| Arbitrary code execution is a high security risk.
[exec()](https://docs.python.org/3/library/methods.html#exec)						|	✓	|	✘	| Arbitrary code execution is a high security risk.
[filter()](https://docs.python.org/3/library/methods.html#filter)					|	✓	|	✓	|
[float()](https://docs.python.org/3/library/methods.html#float)					|	✓	|	✘	| Floating point precision is non-deterministic. This is a consensus failure risk.
[format()](https://docs.python.org/3/library/methods.html#format)					|	✓	|	✓	|
[frozenset()](https://docs.python.org/3/library/methods.html#func-frozenset)		|	✓	|	✓	|
[getattr()](https://docs.python.org/3/library/methods.html#getattr)				|	✓	|	✘	| Arbitrary access to attributes could allow private method execution.
[globals()](https://docs.python.org/3/library/methods.html#globals)				|	✓	|	✘	| Access to global scope methods allows modification of private methods and direct storage mechanisms.
[hasattr()](https://docs.python.org/3/library/methods.html#hasattr)				|	✓	|	✘	| Allows exploration path into security exploit development.
[hash()](https://docs.python.org/3/library/methods.html#hash)						|	✓	|	✘	| Potentially non-deterministic outcomes. Consensus failure risk.
[help()](https://docs.python.org/3/library/methods.html#help)						|	✓	|	✘	| Unnecessary.
[hex()](https://docs.python.org/3/library/methods.html#hex)						|	✓	|	✓	|
[id()](https://docs.python.org/3/library/methods.html#id)							|	✓	|	✘	| Potentially non-deterministic outcomes. Consensus failure risk.
[input()](https://docs.python.org/3/library/methods.html#input)					|	✓	|	✘	| User input not supported.
[int()](https://docs.python.org/3/library/methods.html#int)						|	✓	|	✓	|
[isinstance()](https://docs.python.org/3/library/methods.html#isinstance)			|	✓	|	✓	|
[issubclass()](https://docs.python.org/3/library/methods.html#issubclass)			|	✓	|	✓	|
[iter()](https://docs.python.org/3/library/methods.html#iter)						|	✓	|	✘	| Potential mutation of objects that are only supposed to be interfaced with through particular methods.
[len()](https://docs.python.org/3/library/methods.html#len)						|	✓	|	✓	|
[license](https://docs.python.org/3/library/constants.html#license)					|	✓	|	✘	| Unnecessary.
[list()](https://docs.python.org/3/library/methods.html#func-list)				|	✓	|	✓	|
[locals()](https://docs.python.org/3/library/methods.html#locals)					|	✓	|	✘	| See globals()
[map()](https://docs.python.org/3/library/methods.html#map)						|	✓	|	✓	|
[max()](https://docs.python.org/3/library/methods.html#max)						|	✓	|	✓	|
[memoryview()](https://docs.python.org/3/library/methods.html#func-memoryview)	|	✓	|	✘	| Potentially non-deterministic outcomes. Consensus failure risk.
[min()](https://docs.python.org/3/library/methods.html#min)						|	✓	|	✓
[next()](https://docs.python.org/3/library/methods.html#next)						|	✓	|	✘	| See iter()
[object()](https://docs.python.org/3/library/methods.html#object)					|	✓	|	✘	| See callable()
[oct()](https://docs.python.org/3/library/methods.html#oct)						|	✓	|	✓	|
[open()](https://docs.python.org/3/library/methods.html#open)						|	✓	|	✘	| File I/O not supported.
[ord()](https://docs.python.org/3/library/methods.html#ord)						|	✓	|	✓	|
[pow()](https://docs.python.org/3/library/methods.html#pow)						|	✓	|	✓	|
[print()](https://docs.python.org/3/library/methods.html#print)					|	✓	|	✓	|
[property()](https://docs.python.org/3/library/methods.html#property)				|	✓	|	✘	| Property creation not supported because classes are disabled.
[range()](https://docs.python.org/3/library/methods.html#func-range)				|	✓	|	✓	|
[repr()](https://docs.python.org/3/library/methods.html#repr)						|	✓	|	✘	| Unnecessary and non-deterministic due to memory address as output of this method. This is a consensus failure risk.
[reversed()](https://docs.python.org/3/library/methods.html#reversed)				|	✓	|	✓
[round()](https://docs.python.org/3/library/methods.html#round)					|	✓	|	✓
[set()](https://docs.python.org/3/library/methods.html#func-set)					|	✓	|	✓
[setattr()](https://docs.python.org/3/library/methods.html#setattr)				|	✓	|	✘	| Arbitrary setting and overwriting of Python attributes has storage corruption and private method access implications.
[slice()](https://docs.python.org/3/library/methods.html#slice)					|	✓	|	✘	| Unnecessary.
[sorted()](https://docs.python.org/3/library/methods.html#sorted)					|	✓	|	✓
[staticmethod()](https://docs.python.org/3/library/methods.html#staticmethod)		|	✓	|	✘	| Static methods are not supported because classes are disabled.
[str()](https://docs.python.org/3/library/methods.html#func-str)					|	✓	|	✓
[sum()](https://docs.python.org/3/library/methods.html#sum)						|	✓	|	✓
[super()](https://docs.python.org/3/library/methods.html#super)					|	✓	|	✘	| Super is not supported because classes are disabled
[tuple()](https://docs.python.org/3/library/methods.html#func-tuple)				|	✓	|	✓
[type()](https://docs.python.org/3/library/methods.html#type)						|	✓	|	✓
[vars()](https://docs.python.org/3/library/methods.html#vars)						|	✓	|	✘	| Allows exploration path into security exploit development.
[zip()](https://docs.python.org/3/library/methods.html#zip)						|	✓	|	✓

## Illegal AST Nodes

Similarly, some of the AST (abstract syntax tree) nodes that make up deeper levels of the Python syntax are not allowed. Mainly, the nodes around the async/await features are restricted.

AST Node 				| Reason for Restriction
-						| -
[ast.AsyncFor](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#AsyncFor)			| All async code is invalid in Contracting.
[ast.AsyncmethodDef](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#AsyncmethodDef)	| All async code is invalid in Contracting.
[ast.AsyncWith](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#AsyncWith)			| All async code is invalid in Contracting.
ast.AugLoad				| AST Node never used in current CPython implementation.
ast.AugStore			| AST Node never used in current CPython implementation.
[ast.Await](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#Await)				| All async code is invalid in Contracting.
[ast.ClassDef](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#ClassDef)			| Classes are disabled in Contracting.
[ast.Ellipsis](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#Ellipsis)			| Ellipsis should not be defined in a smart contract. They may be an effect of one.
[ast.GeneratorExp](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#GeneratorExp)		| Generators hold state that is incompatible with Contracting's model.
[ast.Global](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#Global)				| Scope modification could have security implications.
ast.Interactive			| Only available in Python interpreters. Potential security risk.
[ast.MatMult](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#MatMult)				| New AST feature. Not yet widely adopted. Potential security risk.
[ast.Nonlocal](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#Nonlocal)			| Scope modification could have security implications.
ast.Suite				| Similar to ast.Interactive
[ast.Yield](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#Yield)				| Generator related code is not compatible with Contracting.
[ast.YieldFrom](https://greentreesnakes.readthedocs.io/en/latest/nodes.html#YieldFrom)			| Generator related code is not compatible with Contracting.

### Violations

The linter will check for several violations that will fail your smart contract automatically. Here is a list of the current violations and examples of code that will cause them.

#### S1- Illegal contracting syntax type used
Thrown when an AST type that is not allowed is visited by the linter.

```python
def bad():
	2 @ 2 # ast.MatMul code
```

#### S2- Illicit use of '\_' before variable
\_ is used for gating certain functionality. Using it as a prefix to any variable will cause failure

```python
def bad_var():
	_balances = Hash()
```

#### S3- Illicit use of Nested imports
`import` keywords found inside of methods, loops, etc. will fail.

```python
def bad_import():
	import this_wont_fail

	@construct
	def seed():
		import this_will
```
#### S4- ImportFrom compilation nodes not yet supported
Selective importing is not supported and will fail contracts.
```python
def bad_import():
	from token import send
```
#### S5- Contract not found in lib
Not currently used.
#### S6- Illicit use of classes
Classes are not supported in Contracting and their keywords will fail your contract.

```python
def bad_classes():
	class Car:
		def __init__(self, make, model):
			self.make = make
			self.model = model
```
#### S7- Illicit use of Async methods
Any async related code will fail the contract.
```python
def bad_async():
	async def fail_me():
		pass
```
#### S8- Invalid decorator used
`@export` and `@construct` are the only two decorators allowed in Contracting.
```python
def bad_decorator():
	v = Variable()
	@construct
	def seed():
		v.set(100)

	@export
	def get_v():
		return v.get()

	@unknown
	def this_will_fail():
		pass
```
#### S9- Multiple use of constructors detected
Only a single `@construct` can be included in a contract.

```python
def bad_construct():
	v = Variable()
	@construct
	def seed():
		v.set(123)

	@construct
	def seed_2():
		v.set(999)
```
#### S10- Illicit use of multiple decorators
Stacking decorators is not allowed.

```python
def bad_construct():
	v = Variable()
	@export
	@construct
	def seed():
		v.set(777)
```
#### S11- Illicit keyword overloading for ORM assignments
ORM arguments are injected into the \_\_init\_\_ method on runtime. Messing with these will fail your contract.

```python
def bad_var():
	v = Variable(contract='token')
	w = Variable(driver=None)
	x = Variable(another_kwarg='this will fail')

	@export
	def set():
		v.set(777)
		w.set(999)
		x.set(123)
```
#### S12- Multiple targets to ORM definition detected
Python allows multiple assignment. Trying to do a multiple assignment from an ORM object will fail your contract.
```python
def bad_targets():
	x, y = Hash()
	@export
	def set():
		x['stu'] = 100
		y['stu'] = 999
```
#### S13- No valid contracting decorator found
A contract without a single `@export` decorator is invalid.
```python
def bad_export():
	@construct
	def seed():
		pass
```
#### S14- Illegal use of a builtin
Referencing a builtin that is illegal will fail the contract.
```python
def bad_builtin():
	@export
	def credits():
		return credits
```
#### S15- Reuse of ORM name definition in a method definition argument name
Reuse of any ORM names in any loops, methods, etc. will fail the contract.
```python
def bad_var():
	used_once = Variable()

	@export
	def override():
		used_once = 123
```