# Transducers in Ramda

I wanted to better understand the behavior of transducers, so I'm going to try them out in the familiar context of math.

Here's the first Euler Project problem:

> If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23.
> Find the sum of all the multiples of 3 or 5 below 1000.

To make this a little more interesting, lets increment all of these multiples before summing them.

This can be solved in JavaScript using mapping, filtering, and reducing, but combinatorics of other Euler problems usually makes this kind of approach inefficient. Here, the numbers are small, so this first pass works fine:

```javascript
import R from 'ramda'
const numbers = R.range(1, 1001)
const isMult = (x) => (x % 3) * (x % 5) === 0
numbers
  .filter(isMult)
  .map(R.add(1))
  .reduce(R.add, 0) // 234635
```

So, we start with an array, filter it to another array, map it to another array, then reduce that array down to a number. This computation creates two intermediate arrays. For a small problem like this, it's okay, but could really bog down a problem that needs to process millions of numbers.

We can avoid having to create these intermediate arrays by considering that we can reimplement this solution as follows:

```javascript
const multOrZero = (x) => isMult(x) ? x : 0
const addOne = (x) => x === 0 ? x : x + 1
const transform = R.compose(addOne, multOrZero)
numbers.map(transform).reduce(R.add, 0)
```

What I did was define functions that sort of did what the map and filter were doing, but were compatible in a way that they could be composed. By doing things this way, we bring things down to one intermediate array.

But really, we don't need any arrays at all.
