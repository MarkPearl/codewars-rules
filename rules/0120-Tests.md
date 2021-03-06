Tested testing theories
------------------------

Tests are the bread and butter of Codewars. Without tests, you couldn't
check whether the user has solved your kata, so they're very important. Yet
some neglect them.

Make sure to file an issue if you notice that a kata is lacking some tests.
This will prevent a moderator from approving the kata. This will increase the
overall quality of the approved katas. Also, as long as [#123] stands, one
cannot change the tests in approved katas with more than 500 solutions.

 [#123]: https://github.com/Codewars/codewars.com/issues/123


### Make your test descriptive

Let's have a look at the following test:

```javascript
Test.expect(fizzBuzz(15) === 'FizzBuzz', "Value not what's expected")
```
There are several things wrong with it. First of all, it uses `expect`. You
should __never__ use `expect` in any language, __unless__ there isn't a fitting
function in your test framework. But usually, there is a correct testing
function. Here, one would use `Test.assertEquals`, but it might vary, depending
on your language:

```javascript
Test.assertEquals(fizzBuzz(15), 'FizzBuzz', "Value not what's expected")
```

The additional message will be shown if the test failed. However, "Value not
what's expected" is useless. It doesn't give the user any hint what's gone
wrong:

```
  Expected: "FizzBuzz", actual: "Buzz". Value not what's expected.
```
Either drop the message in this case, or use a better one:

```javascript
Test.assertEquals(fizzBuzz(15), 'FizzBuzz', "testing on 15")
```
This tells the user immediately that their function failed on `15`.

#### Group your tests

Furthermore, you should group your tests with `describe` and `it`. After all,
a tests _describes_ the behaviour of something, for example the behaviour
of `fizzBuzz`. _It_ should pass several specifications:

```javascript
Test.describe('fizzBuzz', function(){
  Test.it('should return "Fizz" on 3,6,9,12', function(){
    for(let i = 1; i <= 4; ++i)
      Test.assertEquals(fizzBuzz(3 * i), 'Fizz')
  });

  Test.it('should return "Buzz" on 5,10,20,25', function(){
    for(let i = 1; i <= 4; i += 3)
      Test.assertEquals(fizzBuzz(5 * i),       'Buzz')
      Test.assertEquals(fizzBuzz(5 * (i + 1)), 'Buzz')
  });

  Test.it('should return "FizzBuzz" on 15*n', function(){
    for(let i = 1; i <= 4; ++i)
      Test.assertEquals(fizzBuzz(15 * i), 'FizzBuzz')
  });
});
```
Note that not every language provides `describe` and `it`. Refer to your
language's test framework documentation to find out how to group tests there.

#### Make errors obvious

Especially if you use random tests (see below), you want to make sure that the
user knows _why_ the test failed. You could print the arguments. You could
show a hint. Either way, a user shouldn't be left alone in face of an error.

### Always test all corner cases of your input domain

If you ask the user to create a function that should work for
`1 <= N <=  1,000,000`, make sure that their function works for *both* 1 and
1,000,000. If you specified that "negative input should return a monkey",
make sure that you actually test negative input.

### Use random tests

Whenever when you state a kata you also have to write a solution. This is
great, as you can use exactly that function to check the users code
in the tests. What's even better: it makes you think
about the input domain.

This is usually the hard part of creating random tests. After all, creating
sufficient random input is most often harder than creating the kata itself.
But it is worth every honour.

### Always have some example tests

Unless you're creating a puzzle where the user has to find *the answer*,
present some example tests. Those tests should also contain the corner
cases and some easy random tests, e.g. check that the user actually returns
a number for arbitrary negative numbers.

### Handle floating point values the right way

Whenever the user returns a floating point number, make sure that you take
floating point behaviour into account. The user might not add the numbers
in the same order as you, and therefore end up with a slightly different
number. Equality tests will always fail for that poor user.

Unless you want to write a kata were the user needs to round a floating
point value to a certain precision, never ever tell the user to round,
truncate, floor or ceil a floating point number. Instead, check the
[*relative error*](https://en.wikipedia.org/wiki/Approximation_error). You
might some time need to find a good epsilon, I usually use `1e-12` if I
want to have something "almost exact" and `1e-7` if I want something
"near-ish" the correct solution.

#### An example of floating point dangers

```python
def add_three(a,b,c):
    return a + b + c

def add_three_reference(a,b,c):
    return a + (b + c)

print(add_three_reference(1e-12,1e-12,1) == add_three(1e-12,1e-12,1))
# False
```

#### Consider integral tests

If your number is guaranteed to be an integral number, use `int`, `long`
or your language's equivalent instead of floating point numbers. However, keep
in mind that all values, either input, output, or intermediate should fit in
this type, otherwise you might encounter overflow or underflow issues.
