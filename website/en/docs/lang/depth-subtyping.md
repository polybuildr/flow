---
layout: guide
---

Assume we have two classes, which have a subtype relationship:

```js
class Person { name: string }
class Employee extends Person { department: string }
```

It's valid to use an `Employee` instance where a `Person` instance is expected.

```js
// @flow
class Person { name: string }
class Employee extends Person { department: string }

var employee: Employee = new Employee;
var person: Person = employee; // OK
```

However, it is not valid to use an object containing an `Employee` instance
where an object containing a `Person` instance is expected.

```js
// @flow
class Person { name: string }
class Employee extends Person { department: string }

var employee: { prop: Employee } = { prop: new Employee };
// $ExpectError
var person: { prop: Person } = employee; // Error
```

This is an error because objects are mutable. The value referenced by the
`employee` variable is the same as the value referenced by the `person`
variable.

```js
person.p = new Person;
```

If we write into the `p` property of the `person` object, we've also changed
the value of `employee.p`, which is explicitly annotated to be an `Employee`
instance.

If we prevented any code from ever writing a new value to the object through
the `person` variable, it would be safe to use the `employee` variable. Flow
provides a syntax for this:

```js
// @flow
class Person { name: string }
class Employee extends Person { department: string }

var employee: { prop: Employee } = { prop: new Employee };
var person: { +prop: Person } = employee; // OK
// $ExpectError
person.prop = new Person; // Error!
```

The plus sign indicates that the `p` property is "covariant." Using a covariant
property allows us to use objects which have subtype-compatible values for that
property. By default, object properties are invariant, which allow both reads
and writes, but are more restrictive in the values they accept.

Read more about property variance.
