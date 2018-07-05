# Iterator Methods in JavaScript

## Problem Statement

Many times, when we iterate a collection, we don't really need to keep the
result ("return value") of invoking a function on each element. Maybe something
internal to the element changes, but we don't need to keep track of it.

However, other times we very much **do** care about the result.

For each `roommate` collect `rentTotal / roomates.length` into a jar called
`rentPayment`. For each `roommate` find out whether they paid "July 2018" rent
yet and, if not, invoke the `hassleForRent()` function on them.

Code where we need to iterate across a collection and produce something new as
a result is _very_ common. Instead of using the general `for...of`, JavaScript
has _more expressive_ options called iterator methods. We're going to show how
to tighten up your JavaScript iterations and be more expressive.


## Objectives

1. Define void function
2. Demonstrate `map()`
3. Demonstrate `reduce()`
4. Demonstrate `filter()`
5. Demonstrate `find()`


## Define Void Function

A void function is a function that has no return value. Bear in mind, "no return
value" **does not** mean "no work is done." Consider:

```js
let students = [harryPotter, ronWeasley, hermioneGranger];
for (const student of students) {
  student.eat("dinner");
}
```

In the example, maybe the `.eat()` method increases the `student`'s health
level, but the return value is not something the loop cares to capture.

Recall that `for...of` is _expressive_ for iterating through a collection _and
doing void functions_. Iterator methods, on the other hand, are _expressive_
for iterating through a collection _and making something new from the return
values_.

The most common "iterator methods" are: `map()`, `reduce()`, `filter()`, and
`find()`.


## Demonstrate `map()`

We use `map()` when we want to perform some operation on each element in the
collection, and "gather" the results into a new `Array`.

We'll also use this as an opportunity to demonstrate some of the power of
functions in JavaScript. We'll write `map()` **four times** so you can see how
the iterator functions allow us to write more _expressive_ code.

### `for...of`

```js

let students = [harryPotter, ronWeasley, hermioneGranger, ginevraWeasley];
let patroni = []; // Patroni, sg. patronus

for (const student of students) {
  patroni.push( student.namePatronus() );
}

//=> patroni = ["Stag", "Jack Russell Terrier", "Otter", "Horse"];
```

Here we are using a standard bit of iteration code. Because `for...of` is a
_general_ function that can be used to do lots of things another programmer
won't be sure if the inner workings' return values are important or not.

When we write `.map()` we are saying to other programmers: expect a new array
to come out of this thing after each element is touched! Let's use that more
expressive function name.

### `map()` and a function declaration

```js
function studentPatronusName(student) {
  return student.namePatronus();
}

let students = [harryPotter, ronWeasley, hermioneGranger, ginevraWeasley];
let patroni = students.map(studentPatronusName);
//=> patroni = ["Stag", "Jack Russell Terrier", "Otter", "Horse"];
```

With `map()` we're passing a function _as an argument_. Yes, arguments can be
things like `Number` or `String`...but, in JavaScript, **can also** be
**work**. Very few other programming languages allow that! That's worthy of a
Keanu "Whoa."

![Whoa](https://media.giphy.com/media/ALZ1PPM20REZ2/giphy.gif)

The iterator functions like `map()` expect to be _passed a function as an
argument_ which they will hand each of their elements off to. In the case of
`map()` it hands each element to the function and captures the return value of
the function.

This code is more expressive because it lives up to the promise of map. It
creates a new `Array` after each element is "touched" by a function.

One drawback to this code is that the `studentPatronusName` function doesn't do
much work. It just returns something that the `student` _already_ knew how to
do..  What if we use a function expression ("anonymous function") instead?

### `map` and a function expression

```js
let students = [harryPotter, ronWeasley, hermioneGranger, ginevraWeasley];
let patroni = students.map(function(student) {
  return student.namePatronus();
});
//=> patroni = ["Stag", "Jack Russell Terrier", "Otter", "Horse"];
```

Wow, that's more terse! It has all the same advantages of the previous version.

### `map` and an arrow function

Thanks to arrow functions, we can shorten up the function expression to:

```js
// When the parameter list is only one element, we can drop () !
let students = [harryPotter, ronWeasley, hermioneGranger, ginevraWeasley];
let patroni = students.map( student => student.namePatronus() )
//=> patroni = ["Stag", "Jack Russell Terrier", "Otter", "Horse"];
```

Wow, things are much less verbose! There is much less noisy JavaScript code so
the _expressiveness_ has increased: "`patroni` is the result of `map`-ing
`students` and asking each for the result of its `namePatronus` method".

For the rest of of these examples, we'll use the arrow function.


## Demonstrate `reduce`

The `reduce` function is when we want to get something useful out of each
element in the collection and gather that information into a final summary
object or value. Let's start with a simple numerical example:

```js
let doubledAndSummed = [1, 2, 3].reduce( (total, element) => element * 2 + total, 0)
// => 12
```

Here, for each element, JavaScript passes it and the running total (initialized
to 0, in the second argument to `reduce()`) into the function. The function
multiplies the element by `2` and adds that to the current total.

That sum (`2 * element + total`) is the return value of the function and
becomes the new `total` for the next iteration. When there's nothing left to
iterate, the total is returned.

The initialization value can be left out but it might lead to a real surprise.
If _no_ initial value is supplies, the first element is used _without having
been used in the function_:

```js
let doubledAndSummed = [1, 2, 3].reduce( (total, element) => element * 2 + total)
// => 11
```

The initialization value can be changed:

```js
let doubledAndSummedFromTen = [1, 2, 3].reduce( (total, element) => element * 2 + total, 10)
// => 22
```

For more powerful uses, we could use:

```js

let hogwarts_houses = {
  "Slytherin": [],
  "Gryffindor": [],
  "Hufflepuff": [],
  "Ravenclaw": []
}

/*

Assume sorting_hat.assign() returns a String ("Slytherin", "Gryffindor",
"Hufflepuff", "Ravenclaw") based on the argument passed in.

*/

incoming_students.reduce( (houses, student) => houses[sorting_hat.assign(student)].push(student) , hogwarts_houses)
```

Here we iterate a collection of students and assign each one to a pre-existing
accumulator `Object`. We ask the `Object` to look up an `Array` keyed by the
name of the houses. We then `push()` the student into that `Array`. Later in
the code:

```js
hogwarts_houses["Gryffindor"] //=> [hermioneGranger, ronWeasley, harryPotter]
```

Amazing!

## Demonstrate `filter`

The function `filter` iterates through each member in the collection and if the
passed-in function is true, the member is loaded into a new `Array`.

Here's a simple numerical example:

[1,2,3,4,5].filter( n => n % 2 === 0 ) //=> [2,4]

This code's function _returns_ true if the element can be divided by 2 with no
remainder (i.e. "is even").

More entertainingly:

```js
thirdYears = [ronWeasley, hermioneGranger, harryPotter];
threeBroomSticksVisitors = thirdYears(s => s.parentalFieldTripPermissionOnFile)
// threeBroomSticksVisitors => [ronWeasley, hermioneGranger]
```

## Demonstrate `find`

The `find` function behaves like `filter` because it wants a truthy / falsey
result. However, `find` returns the **first** element for which the function
returns true, a single element.

```js
[1,3,5,6,8].find( e => e % 2 === 0 )
```

Note, you could have multiple matches, but `find` returns _only the first
element_. This could lead to some bugs if you're not careful.

```js
let activeStudents = [hermioneGranger, nevilleLongbottom, harryPotter,
ronWeasley];

activeStudents.find( s => isPossibleSavior(s) )
// => nevilleLongbottom

activeStudents.filter( s => isPossibleSavior(s) )
//=> [nevilleLongbottom, harryPotter]
```

## Conclusion

The iterator methods provide an expressive way to work through a collection and
transform it (`map`), distill something from it (`reduce`), filter it
(`filter`) or find first match (`find`). These functions all utilize that
JavaScript treats functions as "first class data" i.e. things you can pass
around like `Number`s or `String`s.
