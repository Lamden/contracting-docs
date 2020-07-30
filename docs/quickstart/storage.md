## Storing Data

Smart contracts are not interesting without some useful information to store. You can define storage locations with the `Hash` object. Let's see what a ledger looks like.

```python
def ledger():
	accounts = Hash(default_value=0)
	@export
	def mint(account: str, amount: float):
		accounts[account] += amount

	@export
	def send(account: str, amount: float, to: str):
		assert accounts[account] >= amount

		accounts[account] -= amount
		accounts[to] += amount
```

As you can see, creating a `Hash` object is similar to a Python dictionary. The difference is that the information that is set is stored in the smart contract state space. You can easily access it and mutate it using a variety of different methods.

`Hash` objects have the ability to accept up to 16 arguments for 16 dimensions of data. You do not have to indicate the number of dimensions required prior. You can set and get data into hashes this way by seperating your keys with commas in your slice notation like so:

```python
def ledger():
	accounts = Hash(default_value=0)
	@export
	def mint(owner: str, purpose: str, spender: str, amount: float):
		accounts[owner, purpose, spender] += amount

	@export
	def send(owner: str, purpose: str, spender: str, amount: float, \
		to: str, other_purpose: str, other_spender: str):
		assert accounts[owner, purpose, spender] >= amount

		accounts[owner, purpose, spender] -= amount
		accounts[to, other_purpose, other_spender] += amount
```

You can also define 'units' of storage that store a single variable by using the `Variable` keyword. `Variable`s do not take any keys as arguments. Here is the above example using `Variable` objects instead:

```python
def ledger():
	account = Variable()
	reciever = Variable()

	@export
	def mint(amount: float):
		a = account.get()
		if a is None:
			a = 0

		account.set(a + amount)

	@export
	def send(amount: float):
		assert account.get() >= amount

		account.set(account.get() - amount)

		r = reciever.get()
		if r is None:
			r = 0

		reciever.set(r + amount)
```

## Storing Complex Data

All data is encoded in JSON format. This means that you can store 'complex' Python types and pull them out for your own use. Lists, tuples, and dictionaries are supported. Python objects are not supported. However, Timedelta and Datetime types are, which is explained in the 'Stdlib and Extensions' section in 'Key Concepts'.

| Contracting format |	JSON format	|
|--------------------|--------------|
|Python dict		| object	|
|Python list		| array	|
|Python str		| string	|
|Python int		| number (int)	|
|Python Decimal		| object (special)	|
|Python True		| true	|
|Python False		| false	|
|Contracting Timedelta		| object	|
|Contracting Datetime		| object	|

However, you should use Multihashes to create objects rather than storing large objects. Users pay each time they read and write data in your smart contract, so using Multihashes keeps the payload size small and appropriate to the specific transation.

For example, if you wanted to store the following JSON object, you should consider the 2nd example:

```
{
	'thing': {
		'subthing': 'hello',
		'a_list': [1, 2, 3]
	},
	'second_thing': [5, 6, 7, 8]
}
```

#### Bad Way - Very Expensive
```python
def bad_storage():
	thing = Variable()

	@construct
	def seed():
		thing.set({
			'thing': {
				'subthing': 'hello',
				'a_list': [1, 2, 3]
			},
			'second_thing': [5, 6, 7, 8]
		})
```

#### Good Way - Less Expensive
```python
def bad_storage():
	thing = Hash()

	@construct
	def seed():
		thing['thing']['subthing'] = 'hello'
		thing['thing']['a_list'] = [1, 2, 3]

		thing['second_thing'] = [5, 6, 7, 8]
```
Read more about how this works in the 'Storage' section in 'Key Concepts'.