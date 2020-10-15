## Datetime Standard Library

Every contract has a special `datetime` library available to it that mimicks some of the functionality of the Python equivalent. The following objects and constants are available:

### datetime.datetime

```python
d = datetime.datetime(year, month, day, hour=0, minute=0, microsecond=0)
```

`datetime` is a pretty close mapping to the Python Datetime object. It requires the year, month, and day at the very least to initialize. All times are in UTC+0.

#### Comparisons

A valid `datetime` comparison takes another `datetime` on the right side of the comparison. All comparisons return `True` or `False`.

```python
d1 = datetime.datetime(year=2019, month=10, day=10)
d2 = datetime.datetime(year=2019, month=10, day=11)

# LESS THAN
d1 > d2 # False
d1 >= d2 # False

# GREATER THAN
d1 < d2 # True
d1 <= d2 # True

# EQUALITY
d1 == d2 # False
d1 != d2 # True
```

#### Operations

There are only two valid operations for `datetime`. Addition takes a `timedelta` and returns a new `datetime` while subtraction takes another `datetime` and returns a `timedelta`.

_Use addition to add an interval of time to a `datetime`._

_Use subtraction to calculate the interval of time between two `datetime` objects._

```python
d = datetime.datetime(year=2019, month=10, day=10)
t = datetime.timedelta(days=1)

# ADDITION
new_d = d + t # Returns new datetime
expected_new_d = datetime(year=2019, month=10, day=11)

new_d == expected_new_d # True

# SUBTRACTION
new_t = new_d - d # Returns a timedelta. Should reverse the above operation

new_t == t # True
```
### datetime.timedelta

```python
t = datetime.timedelta(weeks=0, days=0, hours=0, minutes=0, seconds=0)
```

`timedelta` is also a pretty close mapping to Python's own timedelta object. It represents an interval of time which can be used to determine expiration dates, etc.

For example, if a transaction occurs on a smart contract 2 weeks after it has been initialized, the transactino can fail. If the transaction occurs within the 2 week interval, it would succeed.

#### Comparisons

Comparisons are between two `timedelta` objects.

```python
t1 = datetime.timedelta(weeks=1, days=1, hours=2)
t2 = datetime.timedelta(weeks=1, days=1, hours=3)

# LESS THAN
t1 > t2 # False
t1 >= t2 # False

# GREATER THAN
t1 < t2 # True
t1 <= t2 # True

# EQUALITY
t1 == t2 # False
t1 != t2 # True
```

#### Operations

`timedelta` operations are between other `timedeltas`, and in one case, `int`. `timedelta` supports addition, subtraction, and multiplication.

_While it's technically possible to multiply `timedelta` objects, it can produce strange results._

```python
t1 = datetime.timedelta(weeks=1)
t2 = datetime.timedelta(weeks=2)

t3 = t1 + t2
t3 == datetime.timedelta(weeks=3) # True

t4 = t2 - t1
t4 == datetime.timedelta(weeks=1) # True

t5 = t1 * 5
t5 == datetime.timedelta(weeks=5) # True

t6 = t1 * t2
t6 == datetime.timedelta(weeks=14) # True
```

### Constants

The following `timedelta` constants are available for you to use.

```python
datetime.WEEKS   == datetime.timedelta(weeks=1)   # True
datetime.DAYS    == datetime.timedelta(days=1)    # True
datetime.HOURS   == datetime.timedelta(hours=1)   # True
datetime.MINUTES == datetime.timedelta(minutes=1) # True
datetime.SECONDS == datetime.timedelta(seconds=1) # True
```

## Now
In the Lamden blockchain, a special variable called `now` is passed into the execution environment. Contracting on its own does not have this variable available. You would have to add it into the environment yourself.

If you always use the `ContractingClient` object you will not have to worry about this problem. The `ContractingClient` adds a `now` variable if you execute methods on an `AbstractContract` that is pulled from the state space.

However, if you use the raw `Executor` object, `now` will not be available. Proceed accordingly.

`now` is a `Datetime` object for when the block that the transaction is in has been submitted to the executors.

```python
def expiration_contract():
	EXPIRATION = datetime.timedelta(days=5)
	submission_time = Variable()

	@construct
	def set_submission_time():
		submission_time.set(now) # Set's variable to when contract was submitted

	@export
	def has_expired():
		if now - submission_time.get() > EXPIRATION:
			return True
		return False
```

The above contract uses `now` in two distinct ways. First, it captures `now` when the contract is submitted and stored it into the state. Second, it references `now`, which will be the current time on subsequent contract executions, and compares it against the original `Datetime` stored.

If the result is greater than the expiration provided as a constant, the contract returns true.
