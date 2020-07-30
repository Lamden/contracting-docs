## Executing methods

A smart contract is not interesting without interaction. This is where the `@export` decorator comes into play. As a smart contract developer, you choose which methods you want to allow other people to call. All methods are private by default. Only methods that have an `@export` decorator above them are callable by an outside party.

To call a method on a smart contract, we have to pull the contract back out from the state space.
```python
hello_world = client.get_contract('hello_world')
hello_world
```
```python
>> <contracting.client.AbstractContract at 0x1130748d0>
```

You can pull contracts out from the state space and into an Abstract Contract object that then allows deeper interaction. The client takes care of creating all of the attributes for you. All exported methods are automatically available on the contract once you pull them.

```python
hello_world.hello()
```
```
>> 'World!'
```

It works!

## Defining methods


## Private methods

We added a private method to the Hello, World! contract as well. Private methods do not have `@export` above them. A user cannot call a private method. However, the smart contract itself can call a private method within itself.

This is how the `add` method actually adds. While contrived, this example illustrates how private methods behave.

## Keyword Arguments

methods that take arguments expect keyword arguments passed in. Positional arguments are not supported and will cause an error.

Here is how we call our `add` method.

```python
hello_world.add(a=2, b=3)
```
```
5
```

Here is what happens if we do not use keyword arguments.

```python
hello_world.add(2, 3)
```
```python
TypeError: _abstract_method_call() got multiple values for argument 'signer'
```

### Try it for yourself

1. Write your own smart contract that has 3 different exported methods.
2. Write a method that is not exported that one of your exported methods calls. 
3. Submit the smart contract into the state space.
4. Pull it back out as an Abstract Contract.
5. Call each method and verify that it works as expected.
