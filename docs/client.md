## What is it?
The `ContractingClient` class is the main programmatic API for interacting with the Contracting library apart from developing smart contracts. It controls the submission, execution, and mocking processes so that you can focus on developing and testing. The `ContractingClient` object is also how programs can execute smart contracts themselves in more customized setups.

The class uses a factory pattern. The developer calls `get_contract` to load a smart contract that has been submitted into state at a prior time. The `ContractingClient` then generates an `AbstractContract` which serves as a wrapper around the actual smart contract code. All of the methods and attributes of the smart contract are dynamically loaded into this new `AbstractContract`. After it has been generated, you call your methods directly on the `AbstractContract` like you would any other Python object or module.

The following is the API reference for the `ContractingClient`:

##### \_\_init__(self, signer, submission_filename, driver, metering, compiler, environment={})
Allows customization of the initial setup. If you want to use a different submission smart contract, for example, you can set the filename here. You can also specify the driver, if metering is enabled, etc.

On default, metering is disabled and the default signer is 'sys'. The rest of the parameters mimic the Lamden blockchain.

##### set_submission_contract(self, filename=None)
If after initialization, you want to change the submission contract, use this function.

##### flush(self)
Deletes all state information and then puts the submission contract back into state. This is used to set up a 'clean' environment in between tests, for example.
    
##### get_contract(self, name)
Returns an `AbstractContract` given a string name. Returns `None` if the contract does not exist.
    
##### closure_to_code_string(self, f)
Internal method that takes a Python function and returns the string that should be submitted to the submission contract's `code` argument. This is useful for generating the exact payload of what to send on the Lamden blockchain when submitting a new smart contract after you have locally tested and developed it.

##### lint(self, f, raise_errors=False)
Checks the smart contract for any violations. If `raise_errors` is `False`, a list of violations will be returned. Otherwise, a Python `Exception` will be raised if any violation occurs.
   
##### compile(self, f)
Take a string or a closure, and returns the compiled code as a string.

##### submit(self, f, name=None, metering=None, owner=None, constructor_args={})
Takes a string or a closure and submits it to state. If a string is provided as `f`, the `name` argument must be a string name. Otherwise, if it is a closure, the name is the name of the function.

##### get_contracts(self)
Returns a list of strings of all contracts currently in state.

##### get_var(self, contract, variable, arguments=[], mark=False)
Gets any arbitrary key in storage where `contract` is the contract name, `variable` is the name of the Python variable (Variable or Hash), and `arguments` are the additional keys provided.

For example,
```python
balances = Hash()
sets = Variable()

@export
def set(account: str, amount: int):
    balances[account] = amount
    
    sets.set(sets.get() + 1)

```

Assuming the contract is named `con_test`, the variable name is either `balances` or `sets` depending on which you want to access. 

For `balances`, you also provide the list of keys. In this case, you would provide `[<account_name]`. If `balances` was a multihash, you would provide a list of the additional keys required to get to the location in the state storage.

For `sets`, no arguments are provided.

`mark` is an internal argument and should not be used.

##### set_var(self, contract, variable, arguments=[], value=None, mark=False)

Follows the same format as `get_var` but sets the variable to any arbitrary amount. Good for mocking in tests.
    
`mark` is an internal argument and should not be used.

## Abstract Contracts
`AbstractContract` objects are what are returned when a contract is retrieved from state. They contain all of the exported functions and available attributes. You can also call the non-exported functions using the special `run_private_function` method. Developers do not create `AbstractContract` objects themselves.

##### \_\_init__(self, name, signer, environment, executor: Executor, funcs)
On default, these are set by the `ContractingClient` object used. The `funcs` argument is a list of strings that the `ContractingClient` parses from the code which indicates all of the functions available in the contract. The initialization function then creates partial functions with the `_abstract_function_call` method. These partial functions are dynamically created and correlated to the smart contract function name so the developer experience feels as natural to regular Python as possible and REPLs can be used.

##### keys(self)
Returns a list of all keys that have been written to on the contract so far.

##### quick_read(self, variable, key=None, args=None)
Similar to the `ContractingClient` method `get_var`, but does not need a contract name. Instead, it takes the `variable` name, and the key, which is the first dimension of the hash. Additional dimension are added in the `args` argument.

Example that expands on the one in `get_var` above:
```python
balances = Hash()
sets = Variable()

@export
def set(account: str, amount: int):
    balances[account] = amount
    
    sets.set(sets.get() + 1)

```

Assuming the contract has been loaded from the `ContractingClient`, `quick_read(variable='balances', key='account')` would be valid.

##### quick_write(self, variable, key=None, value=None, args=None)
Similar to the `ContractingClient` method `set_var`, with the same differences as `quick_read`. 

##### run_private_function(self, f, signer=None, environment=None, **kwargs)
Allows execution of non-exported functions.

##### \_\_getattr__(self, item)
The override of the special Python method. This is called when a 'normal' API method is not successful, a.k.a any method not listed in this document that is either dynamically generated with a partial function, or potentially a variable.

Python will attempt to find a corresponding partial function to execute first. If the smart contract does not have an exported function available, it will assume that the attribute refers to somewhere in storage. If this is not the case, an `Exception` is thrown.

Example:
```python
def con_test():
    balances = Hash()
    sets = Variable()
    
    @export
    def set(account: str, amount: int):
        balances[account] = amount
        
        sets.set(sets.get() + 1)

client = ContractingClient()
client.submit(con_test)

contract = client.get_contract('con_test')

contract.set(account='thing', amount=100)
```

In the example above, `set` is an exported function, but not a function in the `AbstractContract` interface. It is generated by the `ContractingClient`. Thus, after Python realizes this, it looks at the dynamic functions. It finds the `set` function that was created and then passes the keyword arguments.

Another example:
```python
contract.set(account='thing', amount=100)

print(contract.balances['thing'])
>>> 100
```

This time, `balances` is being accessed but is not a function. Therefore, it is assumed that this is somewhere in state. The storage at `balances` is read and if it is not `None`, it is returned. Otherwise, the `Exception` is thrown.

Note: this has the unwanted behavior of throwing `Exceptions` even if the variable is correct, but the storage has not been set before.

##### now(self)
Returns a `DateTime` object from the standard library that correspondes to the local system time. Used to pass as an environment variable in execution.

##### \_abstract_function_call(self, signer, executor, contract_name, func, environment=None, metering=None, now=None, **kwargs)
Internal method for construction partial functions on initialization.
