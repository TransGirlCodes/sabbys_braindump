# Doubles and Floats are bad

Using [[Python]] as an example, Python’s `float` type is a natural first step to represent monetary amounts in the code. Almost all platforms map Python floats to IEEE-754 “double precision”.

Doubles contain 53 bits of precision. When the machine is trying to represent the fractional part (mantissa) of a given number it finds a bit sequence $b_1, b_2, ..., b_{53}$ so that a sum:

$b_1\left(\frac{1}{2}\right)^1 + b_2\left(\frac{1}{2}\right)^2 + ... + b_{53}\left(\frac{1}{2}\right)^{53}$

is close to the number as possible. So, values such as 0.1 cannot be exactly represented. You may find this famous example in official [Python docs](https://docs.python.org/3/tutorial/floatingpoint.html):

```python
>>> .1 + .1 + .1 == .3
False
```

It happens because 0.1 is not exactly 1/10.

Let’s see how it can hinder money operations.

A web shop sells an item for $1.01$. The web shop buys it form its supplier for $0.99$ and the owner wants to calculate a revenue if quintillion items (one with 18 zeros) are sold:

```python
format((1.01 - 0.99)*1e18, '.2f')
>>>  '20000000000000016.00'
```

You expected a 2 with 16 zeros (2 cent revenue per item times 1e18), but there are an additional 16 dollars. It will take a lot of time to sell so many items, but the calculation is wrong anyway.

**More real life example**

You have a deposit of 93 cents in a bank with 2.25% interest rate. You came back in thousand years to withdraw it.

For that you need to calculate a [future value](https://www.investopedia.com/terms/f/futurevalue.asp):

```python
deposit = 0.93
future_value = deposit * ((1 + (0.0225))**1000)
future_value
>>> 4283508449.7111807
```

Almost 4.3 billion. But is it accurate since we are using float here?

**Decimal**

It’s a part of a [standard library](https://docs.python.org/3/library/decimal.html) and provides a representation of real numbers. The web shop revenue calculation is now correct:

```python
from decimal import Decimal
sell_price = Decimal('1.01')
buy_price = Decimal('0.99')
items_sold = Decimal('1e18')
profit = (sell_price - buy_price) * items_sold
profit
>>> Decimal('2E+16')
```

And for the future value.

```python
deposit = Decimal('0.93')
decimal_future_value = deposit * (1 + (Decimal('0.0225')))**Decimal('1000')
decimal_future_value
>>> Decimal('4283508449.711328779924154334')
```

The difference with float in that case is quite small - 4th digit after decimal point. But it’s still present.

**Performance**

Float also appears more fast than Decimal:

```python
In [2]: def float_order(item_price, item_count):
   ...:     return sum([item_price for _ in range(item_count)])
   ...:

In [3]: def decimal_order(item_price, item_count):
   ...:     decimal_item_price = Decimal(item_price)
   ...:     return sum([decimal_item_price for _ in range(item_count)])
   ...:

In [4]: %timeit float_order(1.01, 10000)
297 µs ± 11.6 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)

In [5]: from decimal import Decimal

In [6]: %timeit decimal_order('1.01', 10000)
783 µs ± 19.7 µs per loop (mean ± std. dev. of 7 runs, 1000 loops each)
```

## Solutions

Use integers torepresent the decimal value.

Example in [[R]]
```r
library(bit64)

as.decimal <- function(x, p = 2L) {
	structure(as.integer64(x * 10 ^ p), class = "decimal", precision = p)
} 

print.decimal <- function(x) print(as.double(x))

as.integer64.decimal <- function(x) structure(x, class = "integer64", precision = NULL) # this should not be exported

as.double.decimal <- function(x) as.integer64(x)/(10^attr(x, "precision"))

is.decimal <- function(x) inherits(x, "decimal")

"==.decimal" <- function(e1, e2) `==`(as.integer64(e1), as.integer64(e2))

"+.decimal" <- function(e1, e2) `+`(as.integer64(e1), as.integer64(e2))

d <- as.decimal(12.69)
is.decimal(d)
#[1] TRUE
print(d)
#[1] 12.69
as.double(d)
#[1] 12.69
d + as.decimal(0.9)
#[1] 13.59
0.1 + 0.2 == 0.3
#[1] FALSE
as.decimal(0.1) + as.decimal(0.2) == as.decimal(0.3)
#[1] TRUE
```