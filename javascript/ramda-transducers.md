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

We can use transducers to avoid creating these intermediate arrays. First we define a function that can take elements of `numbers` and do the following:

1. If it is a multiple of 3 or 5, ignore it. Else, move it forward.
2. Add 1 to that number.
3. Add the result to the running total.

```javascript

```
