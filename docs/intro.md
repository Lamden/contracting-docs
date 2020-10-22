## What is Contracting?
Contracting is a system that brings the ease of Python into the complex world of smart contracts and distributed systems. Here's how it looks:
```python
balances = Hash()
owner = Variable()

@construct
def seed():
    owner.set(ctx.caller)
    balances[ctx.caller] = 1_000_000

@export
def transfer(amount: float, to: str):
    sender = ctx.signer
    assert balances[sender] >= amount, 'Not enough coins to send!'

    balances[sender] -= amount

    if balances[to] is None:
        balances[to] = amount
    else:
        balances[to] += amount

@export
def balance(account: str):
    return balances[account]

```

### Applications
1. Contracting is the language that is used in the Lamden blockchain system and you want to develop smart contracts for that blockchain system.
2. You want to deploy your own instance of a database that uses smart contract 'apps' to control traditional CRUD type operations.
3. You really like Python and want to learn as much as possible.

### Value Proposition

Contracting focuses on developer experience (DX) which is a major focus of the Python language as a whole.

Our goal is to create a development experience that is clear, concises, and manageable so that you don't have to worry about what makes smart contracts hard, and just have to worry about what makes your smart contract great.

We take inspiration from some of these Python libraries:

* Requests
* Keras
* PyTorch

## Motivation
If you look at the current market of blockchain smart contracting languages, most take a hard right turn into the 'security through obscurity' lane. The rationale is that if you use highly functional and low level languages, it is harder to make errors because the type systems are so strict.

* * *

Tezos says this about their own language [Michelson](https://www.michelson-lang.com/why-michelson.html):

```
At first sight, Michelson is a strange language. 
It doesn’t include many features like polymorphism, closures, or named functions. 
Compared to a language like Haskell or OCaml, it seems underpowered; 
its stack is not always easy to deal with; there is no standard library.
```

*Why would you ever choose to use this language for your newest application?*

* * *

This makes sense, but it is also at the cost of developer adoption. People have a hard time understanding blockchain as it is. Why make it even harder for them to explore and get started?

We believe this concept is like a walled garden. It looks great if you are on the inside, but no one can hop over the fence without investing hundreds of hours into learning the platform.

This makes it extremely hard for businesses to justify creating proof of concepts and trying out this new technology. As a business owner, how can one justify spending their workers to invest hundreds of hours to simply evaluate if the technology makes sense for the business?

## Basic Philosophy
We believe that all people have the ability to create great software. After all, software development is simply a means of translating a great idea into an actual application that can bring value to the world.

This is true whether the application is for your profession or simply a passion project. Software should empower, not isolate. Thus, it is extremely important for us to create a system that is easy to use and to translate ideas from the mind into a workable application.

Some will argue that this could allow for sub-par development to occur on a blockchain network. After all, many Ethereum smart contracts have been hacked because of buggy smart contracts.

While it is true that 'poor' quality code could be deployed on the Lamden blockchain, this is also true for smart contracting systems that use functional languages as a barrier of entry.

The claim is that functional languages are easier to analyze for 'correctness,' which simply means that a method will have the effects that you desire. However, no language gaurds against actual bugs.

Actual bugs come from a develop not architecting their system against edge cases. Any system can validate and protect against obfuscated data. Only a purposely designed system has checked each edge case when transitioning through states, accepting input from unauthorized parties, etc.

Lamden defends against actual bugs by integrating with Python's existing suites of testing tools. Because Contracting is Python, you can develop Contracting code in any Python IDE and write full unit and integration tests using the testing framework you like: Unittests, Nose, Pytest, and more are all supported.

No other blockchain smart contracting system is already natively integrated with the entire stack of another major language.
