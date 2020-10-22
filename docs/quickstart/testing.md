## Testing and Feedback

As smart contracts get more and more complex, you need to be able to test them to make sure that they are doing what they are supposed to do. This becomes especially important once you start adding storage variables and methods that execute based on the person who is calling them. These will be explained later.

However, for now, we can write a simple smart contract and test to make sure it works.

```python
def test_me():
	@export
	def call_this(a: int):
		return complex_method(a)

	def complex_method(a):
		if a > 50:
			return 'Quack!'
		elif a < 10:
			return 'Oink!'
		elif a == 15:
			return 'Woof!'
		else:
			return 'Meow!'
```

We will use Python's built-in `unittest` library. You can read how to use it [here](https://docs.python.org/3/library/unittest.html).

This is the first part of our test script.

```python
from unittest import TestCase
from contracting.client import ContractingClient

def test_me():
	@export
	def call_this(a: int):
		return complex_method(a)

	def complex_method(a):
		if a > 50:
			return 'Quack!'
		elif a < 10:
			return 'Oink!'
		elif a == 15:
			return 'Woof!'
		else:
			return 'Meow!'

class TestSmartContract(TestCase):
	def setUp(self):
		self.c = ContractingClient()
		self.c.flush()

		self.c.submit(test_me)
		self.test_me = self.c.get_contract('test_me')

	def tearDown(self):
		self.c.flush()
```

Key things that are happening:

* We import the client.
* We define our smart contract in a closure.
* We override the `setUp` and `tearDown` methods in `TestCase` which execute before and after every test respectively. This gives us a clean state to work upon for each test. 

Before each test, we completely flush and resubmit the contract. After each test, we flush again. This is for sanity.

Now let's write the actual tests.
```python
	def test_a_over_50_returns_quack(self):
		self.assertEqual(self.test_me.call_this(a=51), 'Quack!')

	def test_a_under_10_returns_oink(self):
		self.assertEqual(self.test_me.call_this(a=9), 'Oink!')

	def test_a_is_15_returns_woof(self):
		self.assertEqual(self.test_me.call_this(a=15), 'Woof!')

	def test_a_other_cases_returns_meow(self):
		self.assertEqual(self.test_me.call_this(a=50), 'Meow!')

	def test_a_fails_if_non_int_passed(self):
		with self.assertRaises(ValueError):
			self.test_me.call_this(a='Howdy!')
```

The tests are pretty straightforward. Each branch of logic gets it's own test and the behavior is described. You can use whatever testing methods you'd like. We also include a negative test case as an example of how to test that something fails.

## Mocks

Your tests may require different senders or different envionments to complete. You can do this by passing different arguments into the abstract functions.

### Mocking a Signer
When you call a function on a smart contract, pass a string to the `signer` keyword argument.

```python
def token():
    balances = Hash(default_value=0)
    
    @construct
    def seed():
        balances['stu'] = 123
        
    @export
    def get_balance_of_caller():
        return balances[ctx.caller]
```

If you wanted to assert that `stu` owned 123 tokens but `jeff` owned 0, you would write two tests like this:

```python
client = ContractingClient()
client.submit(token)
token_contract = client.get_contract('token')

class Tests(unittest.Testcase):
    def test_stu_has_123(self):
        stu_bal = token_contract.get_balance_of_caller(signer='stu')
        self.assertEqual(stu_bal, 123)
        
    def test_jeff_has_0(self):
        jeff_bal = token_contract.get_balance_of_caller(signer='jeff')
        self.assertEqual(jeff_bal, 123)
```

### Mocking the Enviornment

You can include any arbitrary objects in a dictionary called `env` and pass that into a smart contract function. You use this to mock dates for contracts that deal with time and randoms for contracts that use the pseudorandom module.

This also allows you to override any keywords such as `now` that are available at runtime.

#### Time

Assume this as a test contract.

```python
from contracting.stdlib.bridge.time import Datetime
from contracting.client import ContractingClient
import unittest

def test():
    @export
    def is_2020():
        return now == 2020

class TestSmartContract(unittest.TestCase):
    def setUp(self):
        self.c = ContractingClient()
        self.c.flush()

        self.c.submit(test)
        self.test = self.c.get_contract('test')

    def tearDown(self):
        self.c.flush()
        
    def test_is_2020_true(self):
        env = {'now': Datetime(year=2020)}
   
        res = self.test.is_2020(environment=env)
        self.assertTrue(res)
        
    def test_is_2020_false(self):
        env = {'now': Datetime(year=2019)}
   
        res = self.test.is_2020(environment=env)
        self.assertFalse(res)
```

#### Randoms

Random numbers are 'pseudorandom'